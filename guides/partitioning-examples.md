
## Common Patterns and Examples

### Example 1: Basic Numeric Partitioning

```typescript
{
  slug: "load_ta_from_tw",
  description: "Load test attempts partitioned by ID",
  method: "query",
  methodConfig: {
    querySlug: "SQL_05A_ATTEMPTS_FROM_TW_ID"
  },
  partitionOut: {
    key: "attempt_id",
    field: "attempt_id",
    partSize: 10000
  },
  dependencySourcings: [
    {
      param: "tw_ids",
      type: "job-config",
      config: { configSlug: "pipeline_config", param: "test_window_ids" }
    }
  ]
}
```

### Example 2: Processing Partitioned Data

```typescript
{
  slug: "trfm_attempt_scores",
  description: "Calculate scores for partitioned attempts",
  method: "transform",
  methodConfig: {
    transformSlug: "map-col",
    df_input: "attempts",
    col_output: "total_score",
    source_cols: ["score1", "score2"],
    operation: "add"
  },
  partitionBy: {
    key: "attempt_id",
    concatOut: false  // Keep partitioned
  },
  dependencySourcings: [
    {
      param: "attempts",
      type: "dataframe",
      config: { asset: "load_ta_from_tw" }
    }
  ]
}
```

### Example 3: Categorical Partitioning

```typescript
{
  slug: "load_attempts_by_school",
  description: "Load attempts partitioned by school",
  method: "query",
  partitionOut: {
    key: "school_id",
    field: "school_id",
    categorical: true
  }
}
```

### Example 4: Repartitioning

```typescript
{
  slug: "trfm_by_school",
  description: "Repartition attempts by school for school-level processing",
  method: "transform",
  partitionBy: {
    key: "attempt_id"  // Input partitions
  },
  partitionOut: {
    key: "school_id",  // Output partitions
    field: "school_id",
    categorical: true
  }
}
```

### Example 5: Final Concatenation

```typescript
{
  slug: "trfm_final_output",
  description: "Final transform, output as single file",
  method: "transform",
  partitionBy: {
    key: "attempt_id",
    concatOut: true  // Concatenate all partitions
  }
}
```

## Real-World Examples from Codebase

The following examples are taken from actual asset definitions in `assets.ts` to illustrate both correct and problematic patterns.

### ✅ Positive Example 1: Proper Early Partitioning with Matching Chunk Size

**Good:** `load_taqr` asset partitions immediately after loading, with `partSize` matching `chunkSize`:

```typescript
{
  slug: 'load_taqr',
  description: "Reduced fields of all question responses for all test attempts.",
  structure: "dataframe",
  method: "query-chunked",
  methodConfig: {
    querySlug: 'SQL_05B_TAQR_FROM_TA_ID',
    chunkedParam: 'ta_ids',
    chunkSize: 500,  // Matches partSize below
  },
  partitionOut: {
    key: 'partby_ta_ids',
    field: 'ta_id',
    partSize: 500  // ✅ Matches chunkSize - good alignment
  },
  dependencySourcings: [
    {
      param: "ta_ids",
      type: "asset-col",
      config: {
        asset: "load_ta_from_tw",
        col: "attempt_id"
      }
    }
  ]
}
```

**Why This Works:**
- Partitions immediately after loading (early partitioning)
- `partSize` matches `chunkSize`, ensuring efficient processing
- Uses appropriate partition size (500) for query-chunked operations
- Creates partition scheme that downstream assets can use

### ✅ Positive Example 2: Maintaining Partitioned State Through Pipeline

**Good:** `trfm_item_responses` processes partitioned data and keeps it partitioned:

```typescript
{
  slug: 'trfm_item_responses',
  description: "Unfiltered item responses including score, weight, and formatted responses.",
  structure: "dataframe",
  method: 'transforms',
  methodConfig: {
    sequence: [
      {
        kind: "join",
        df_output: "df",
        config: {
          how: "left",
          left: "df",
          right: "coded_responses",
          left_on: ["item_id", "formatted_response", "lang"],
          right_on: ["item_id", "formatted_response", "lang"],
        }
      },
    ],
    output: "df"
  },
  partitionBy: {
    key: 'partby_ta_ids',  // ✅ Matches upstream partitionOut.key
    concatOut: false,       // ✅ Keeps data partitioned for downstream
  },
  dependencySourcings: [
    {
      param: "load_taqr",
      type: "dataframe",
      config: {
        asset: "load_taqr",  // Uses partitioned asset from Example 1
        cols: undefined,
      }
    },
    // ... other dependencies
  ],
}
```

**Why This Works:**
- `partitionBy.key` matches upstream `partitionOut.key` (`partby_ta_ids`)
- `concatOut: false` preserves partitioning for downstream assets
- Processes each partition independently, reducing memory usage
- Enables parallel processing opportunities

### ✅ Positive Example 3: Final Concatenation When Needed

**Good:** `trfm_item_responses_td` concatenates at the final step when single output is required:

```typescript
{
  slug: 'trfm_item_responses_td',
  scopes: ["test-centres"],
  description: "Item responses with test design info",
  structure: "dataframe",
  partitionBy: {
    key: 'partby_ta_ids',  // ✅ Matches upstream
    concatOut: true,       // ✅ Concatenates for final output
  },
  method: 'transforms',
  methodConfig: {
    sequence: [
      {
        kind: "join",
        // ... join configuration
      },
    ],
  },
  dependencySourcings: [
    {
      param: "load_taqr",
      type: "dataframe",
      config: {
        asset: "load_taqr"
      }
    },
    // ... other dependencies
  ],
}
```

**Why This Works:**
- Uses `concatOut: true` only when final output must be a single file
- Processes partitioned data efficiently, then concatenates at the end
- Appropriate for final outputs or when downstream needs full dataset

### ✅ Positive Example 4: Repartitioning for Different Grouping

**Good:** `trfm_item_responses_nf_omit` repartitions from numeric to categorical:

```typescript
{
  slug: "trfm_item_responses_nf_omit",
  description: "Item responses filtered and repartitioned by test form",
  structure: "dataframe",
  partitionBy: {
    key: "partby_ta_ids",  // ✅ Processes input partitions
    concatOut: false
  },
  partitionOut: {  // ✅ Repartitions by new scheme
    key: "test_form",
    field: "test_design_id",
    categorical: true,  // ✅ Categorical partitioning for natural grouping
  },
  method: "transforms",
  methodConfig: {
    sequence: [
      {
        kind: "filter-col",
        df_output: "nf_omit",
        config: {
          df_input: "nf_omit",
          col: "is_human_scored",
          comparison: "equals",
          value: 0
        }
      },
      // ... more transforms
    ],
    output: "nf_omit"
  },
  dependencySourcings: [
    {
      param: "nf_omit",
      type: "dataframe",
      config: {
        asset: "trfm_item_responses_nf_omit"  // Upstream partitioned asset
      }
    }
  ]
}
```

**Why This Works:**
- Repartitions from numeric (`partby_ta_ids`) to categorical (`test_form`)
- Uses categorical partitioning for natural data boundaries (test forms)
- Maintains partitioning throughout pipeline
- Enables form-level processing downstream

### ❌ Negative Example 1: Missing Partitioning on Large Dataset

**Problem:** `load_ta_from_tw` loads all test attempts for a test window without partitioning:

```typescript
{
  slug: "load_ta_from_tw",
  scopes: ["schools"],
  description: "Loads all test attempts for a test window.",
  structure: "dataframe",
  method: "query",
  methodConfig: {
    querySlug: "SQL_05A_ATTEMPTS_FROM_TW_ID"
  },
  // ❌ Missing partitionOut - could cause memory issues for large test windows
  dependencySourcings: [
    {
      param: "tw_ids",
      type: "job-config",
      config: {
        configSlug: "pipeline_config",
        param: "test_window_ids"
      }
    },
    // ... other dependencies
  ]
}
```

**Why This Is Problematic:**
- Test windows can contain 100,000+ attempts
- Entire dataset loaded into memory at once
- Downstream assets that depend on this must also handle full dataset
- Risk of out-of-memory errors for large test windows
- Prevents efficient parallel processing

**Better Approach:**
```typescript
{
  slug: "load_ta_from_tw",
  // ... other config ...
  partitionOut: {
    key: "attempt_id",
    field: "attempt_id",
    partSize: 10000  // ✅ Partition by attempt ID
  },
  // ... rest of config
}
```

### ❌ Negative Example 2: Missing Partitioning on Potentially Large Query

**Problem:** `load_test_attempts_from_uid` loads attempts for potentially many students without partitioning:

```typescript
{
  slug: "load_test_attempts_from_uid",
  description: "Loads all test attempts for enrolled students",
  structure: "dataframe",
  method: "query-chunked",
  methodConfig: {
    querySlug: "SQL_05A_ATTEMPTS_FROM_UID",
    chunkSize: 1000,
    chunkedParam: "uids",
    makeDistinct: true,
  },
  // ❌ Missing partitionOut - even though using query-chunked
  dependencySourcings: [
    {
      param: "uids",
      type: "job-config",
      config: {
        configSlug: "pipeline_config",
        param: "student_uids"
      }
    }
  ]
}
```

**Why This Is Problematic:**
- Uses `query-chunked` but doesn't partition output
- For large student lists, could load 100,000+ attempts into memory
- Downstream assets like `load_taqr` that depend on this must handle full dataset
- Defeats the purpose of chunked queries if output isn't partitioned

**Better Approach:**
```typescript
{
  slug: "load_test_attempts_from_uid",
  // ... other config ...
  partitionOut: {
    key: "ta_id",
    field: "ta_id",
    partSize: 10000  // ✅ Partition output even though input is chunked
  },
  // ... rest of config
}
```

### ❌ Negative Example 3: Partition Key Mismatch

**Problem:** Downstream asset uses different partition key than upstream:

```typescript
// Upstream asset
{
  slug: "load_taqr",
  partitionOut: {
    key: 'partby_ta_ids',  // Partition key
    field: 'ta_id',
    partSize: 500
  }
}

// Downstream asset - WRONG
{
  slug: "trfm_item_responses",
  partitionBy: {
    key: 'partby_student_ids',  // ❌ Mismatch! Should be 'partby_ta_ids'
    concatOut: false
  },
  dependencySourcings: [
    {
      param: "load_taqr",
      type: "dataframe",
      config: {
        asset: "load_taqr"  // Uses asset with 'partby_ta_ids'
      }
    }
  ]
}
```

**Why This Is Problematic:**
- Will cause runtime error: `partition partby_student_ids does not exist`
- Partition keys must match exactly between `partitionOut.key` and `partitionBy.key`
- Breaks the partition chain in the pipeline

**Correct Approach:**
```typescript
{
  slug: "trfm_item_responses",
  partitionBy: {
    key: 'partby_ta_ids',  // ✅ Matches upstream partitionOut.key
    concatOut: false
  },
  // ... rest of config
}
```

### ❌ Negative Example 4: Premature Concatenation

**Problem:** Concatenating too early, losing partitioning benefits:

```typescript
// Upstream - partitioned
{
  slug: "load_taqr",
  partitionOut: {
    key: 'partby_ta_ids',
    field: 'ta_id',
    partSize: 500
  }
}

// Downstream - WRONG: concatenates too early
{
  slug: "trfm_early_transform",
  partitionBy: {
    key: 'partby_ta_ids',
    concatOut: true,  // ❌ Concatenates too early
  },
  // ... transforms
}

// Later downstream - now must handle full dataset
{
  slug: "trfm_later_transform",
  // ❌ No partitionBy - must load entire concatenated dataset
  dependencySourcings: [
    {
      param: "data",
      type: "dataframe",
      config: {
        asset: "trfm_early_transform"  // Now unpartitioned
      }
    }
  ]
}
```

**Why This Is Problematic:**
- Loses memory benefits of partitioning early in pipeline
- Forces later assets to process full dataset
- Increases memory usage for subsequent transforms
- Prevents parallel processing opportunities

**Better Approach:**
```typescript
{
  slug: "trfm_early_transform",
  partitionBy: {
    key: 'partby_ta_ids',
    concatOut: false,  // ✅ Keep partitioned
  },
  // ... transforms
}

// Later - can still process partitioned
{
  slug: "trfm_later_transform",
  partitionBy: {
    key: 'partby_ta_ids',  // ✅ Still partitioned
    concatOut: true  // ✅ Only concat at final step
  },
  // ... rest of config
}
```

### ✅ Positive Example 5: Proper Partition Chain

**Good:** Complete chain showing proper partitioning flow:

```typescript
// Step 1: Load and partition immediately
{
  slug: "load_taqr",
  method: "query-chunked",
  partitionOut: {
    key: 'partby_ta_ids',
    field: 'ta_id',
    partSize: 500
  }
}

// Step 2: Process partitioned data, keep partitioned
{
  slug: "trfm_item_responses",
  method: "transforms",
  partitionBy: {
    key: 'partby_ta_ids',
    concatOut: false  // ✅ Keep partitioned
  },
  dependencySourcings: [
    {
      param: "load_taqr",
      type: "dataframe",
      config: { asset: "load_taqr" }
    }
  ]
}

// Step 3: Continue processing partitioned
{
  slug: "trfm_item_responses_with_scores",
  method: "transforms",
  partitionBy: {
    key: 'partby_ta_ids',
    concatOut: false  // ✅ Still partitioned
  },
  dependencySourcings: [
    {
      param: "item_responses",
      type: "dataframe",
      config: { asset: "trfm_item_responses" }
    }
  ]
}

// Step 4: Final output - concatenate only at end
{
  slug: "trfm_final_output",
  method: "transforms",
  partitionBy: {
    key: 'partby_ta_ids',
    concatOut: true  // ✅ Concatenate only at final step
  },
  dependencySourcings: [
    {
      param: "data",
      type: "dataframe",
      config: { asset: "trfm_item_responses_with_scores" }
    }
  ]
}
```

**Why This Works:**
- Partitions immediately after loading
- Maintains partitioning through entire pipeline
- Only concatenates at final step when needed
- Maximizes memory efficiency
- Enables parallel processing opportunities
