# Partitioning Guide

## Overview

Partitioning is a critical technique for managing memory usage and enabling processing of large datasets in the Data Lakehouse DAG system. By breaking large datasets into smaller, manageable chunks, partitioning allows the system to process data that would otherwise exceed available memory.

## Why Partition?

### Memory Management

- **Prevents Memory Overruns**: Large datasets can cause out-of-memory errors
- **Enables Large-Scale Processing**: Process datasets larger than available RAM
- **Reduces Peak Memory Usage**: Only load one partition at a time
- **Improves Reliability**: Prevents crashes from memory exhaustion

### Performance Benefits

- **Faster Joins and Sorts**: Joins and sorts are the primary performance drivers for partitioning. Smaller partitioned datasets enable more efficient join operations and sorting.
- **Parallel Processing**: Process partitions independently (future enhancement)
- **Faster Transformations**: Smaller datasets process faster
- **Better Caching**: Cache only needed partitions
- **Reduced I/O**: Read/write smaller files

## When to Use Partitioning

### Always Partition When:

1. **Large Datasets**: Any asset processing >100,000 records
   - **Multiplier Concept**: Assets can have a weight/multiplier assigned to them. If the estimated record weight reaches 1, partitioning is mandatory. This accounts for data multiplication downstream (e.g., one test attempt may have 50+ responses, so partitioning by UID rather than attempt_id may be necessary).
2. **Memory-Intensive Operations**: Complex joins, group-bys, or aggregations
3. **Downstream Partitioning**: When downstream assets need partitioned input
4. **Concurrent Job Risk**: When multiple jobs may run simultaneously

### Consider Partitioning When:

1. **Medium Datasets**: 50,000-100,000 records (depends on record size)
2. **Complex Transforms**: Multi-step transformations
3. **Uncertain Growth**: Datasets that may grow over time
4. **Resource Constraints**: Limited memory available

### Don't Partition When:

1. **Small Datasets**: <10,000 records (overhead not worth it)
2. **Simple Operations**: Single-pass, lightweight transforms
3. **Final Outputs**: When final output must be a single file (unless using `concatOut`)

**Note**: All of these conditions should be met before deciding not to partition. If any condition is not met (e.g., dataset is small but requires complex operations), partitioning may still be necessary.

## Partitioning Constraints

### Join Constraints

**Risk**: Joining a non-partitioned asset to a partitioned one can lead to:
- Record duplication
- Memory overrun
- Incorrect results

**Requirement**: When joining partitioned assets, ensure partition keys align with join keys, or use appropriate repartitioning strategies.

### Aggregation and Grouping Constraints

**Critical Requirement**: Grouping and aggregation operations **must** have all relevant records within the same partition. Records that need to be grouped together must be in the same partition.

**Example of Incorrect Partitioning:**
- Partitioning by `attempt_id` but grouping by `school_id` will fail because records for the same school are spread across multiple partitions
- This was the root cause of recent Alberta memory issues

**Solution**: Partition by the field you will group by, or repartition before grouping operations.

## Types of Partitioning

### Numeric Partitioning

Partitions data by numeric ranges using a fixed partition size.

**Use Cases:**
- Sequential IDs (attempt_id, student_id, etc.)
- Timestamps (when converted to numeric)
- Any numeric field with good distribution

**What is "Good Distribution"?**
Good distribution means the values are **uniformly distributed** across the numeric range, allowing partitions to be roughly equal in size.

**Examples of Good Distribution:**
- **Student IDs**: Good - Most students registered around the same time, creating uniform distribution
- **Timestamps**: Good - Generally uniform over time periods

**Examples of Poor Distribution:**
- **Item IDs**: Borderline/Bad - May have "clumping" or uneven chunks (e.g., very old items clustered together, newer items in different ranges). This makes it difficult to find a good partition size that doesn't create some huge chunks and some tiny ones.

**Configuration:**
```typescript
{
  partitionOut: {
    key: "attempt_id",      // Partition key identifier
    field: "attempt_id",    // Field to partition by
    partSize: 10000         // Records per partition
  }
}
```

**How It Works:**
- Divides field value by `partSize` and floors the result
- Creates partitions like: `attempt_id-0`, `attempt_id-10000`, `attempt_id-20000`
- Each partition contains records where `Math.floor(field / partSize) * partSize` is the same

**Example:**
```typescript
// Records with attempt_id 0-9999 → partition "attempt_id-0"
// Records with attempt_id 10000-19999 → partition "attempt_id-10000"
// Records with attempt_id 20000-29999 → partition "attempt_id-20000"
```

**Choosing Partition Size:**
- **Small (5,000-10,000)**: For very large datasets or memory-constrained environments
- **Medium (10,000-25,000)**: Default for most use cases
- **Large (25,000-50,000)**: For smaller datasets or when memory is abundant
- **Very Large (>50,000)**: Rarely recommended, defeats purpose of partitioning

### Categorical Partitioning

Partitions data by unique categorical values, treating each category as an individual processing unit.

**Use Cases:**
- School-based data (partition by school_id as categorical unit)
- District-based data (partition by district_id as categorical unit)
- Form-based data (partition by form_code as categorical unit)
- Any field with distinct categorical values where you need to process each category independently

**Important Distinction:**
- **Categorical partitioning** treats `school_id` as individual school units (one partition per school) - used when you need to process each school independently
- **Numeric partitioning** by `school_id` would treat it as a numeric range (e.g., schools 1-1000 in partition 1, 1001-2000 in partition 2) - rarely used for school_id

**When to Use Categorical:**
- When you need to process each category independently
- When categories represent natural data boundaries
- When downstream processing requires data grouped by category

**Configuration:**
```typescript
{
  partitionOut: {
    key: "school_id",        // Partition key identifier
    field: "school_id",      // Field to partition by
    categorical: true        // Enable categorical partitioning
  }
}
```

**How It Works:**
- Creates one partition per unique value in the field
- Partition names: `school_id-123`, `school_id-456`, etc.
- Each partition contains all records for that categorical value

**Example:**
```typescript
// All records with school_id=123 → partition "school_id-123"
// All records with school_id=456 → partition "school_id-456"
// All records with school_id=789 → partition "school_id-789"
```

**When to Use Categorical:**
- Natural data boundaries (schools, districts, forms)
- Downstream processing needs data grouped by category
- When number of categories is reasonable (<1000)
- When partitions will be processed independently

## Partitioning Configuration

### Partition Out

Use `partitionOut` when an asset produces partitioned data:

```typescript
{
  slug: "load_attempts",
  method: "query",
  partitionOut: {
    key: "attempt_id",
    field: "attempt_id",
    partSize: 10000
  }
}
```

**Key Properties:**
- `key`: Unique identifier for this partition scheme (used by downstream assets)
- `field`: Column name to partition by
- `partSize`: Number of records per partition (numeric only)
- `categorical`: Boolean flag for categorical partitioning

### Partition By

Use `partitionBy` when an asset processes already-partitioned data:

```typescript
{
  slug: "trfm_attempt_scores",
  method: "transform",
  partitionBy: {
    key: "attempt_id",       // Must match upstream partitionOut.key
    concatOut: false         // Keep output partitioned
  }
}
```

**Key Properties:**
- `key`: Must match the `key` from upstream `partitionOut`
- `concatOut`: If `true`, concatenates all partitions into single output

**Upstream Responsibility**: Partitioning should occur in the **upstream asset** to satisfy the requirements of the downstream consumer. If a downstream asset needs partitioned input, the upstream asset must use `partitionOut` to create those partitions.

### Repartitioning

Use `repartitionOut` to change partition scheme:

```typescript
{
  slug: "trfm_by_school",
  method: "transform",
  partitionBy: {
    key: "attempt_id"        // Process input partitions
  },
  partitionOut: {
    key: "school_id",        // Create new partition scheme
    field: "school_id",
    categorical: true
  }
}
```

**How It Works:**
1. Loads each input partition (attempt_id partitions)
2. Repartitions by new scheme (school_id)
3. Combines records from multiple input partitions into output partitions
4. Stores new partition scheme

## Partitioning Patterns

### Pattern 1: Early Partitioning

Partition as early as possible in the pipeline:

```
load_attempts (partitionOut: attempt_id, partSize: 10000)
    ↓
trfm_attempt_scores (partitionBy: attempt_id)
    ↓
trfm_aggregate (partitionBy: attempt_id)
    ↓
concatPartitionedAsset (if final output needed)
```

**Benefits:**
- Reduces memory usage throughout pipeline
- Enables parallel processing opportunities
- Keeps data partitioned as long as possible

### Pattern 2: Categorical Repartitioning

Partition by ID early, repartition by category later:

```
load_attempts (partitionOut: attempt_id, partSize: 10000)
    ↓
trfm_attempt_scores (partitionBy: attempt_id)
    ↓
trfm_by_school (repartitionOut: school_id, categorical: true)
    ↓
trfm_school_summary (partitionBy: school_id)
```

**Benefits:**
- Efficient initial partitioning by sequential ID
- Natural grouping for downstream processing
- Independent processing of categories

### Pattern 3: Partitioned to Unpartitioned

Process partitioned data, output single file:

```
load_attempts (partitionOut: attempt_id)
    ↓
trfm_attempt_scores (partitionBy: attempt_id, concatOut: true)
```

**Use When:**
- Final output must be single file
- Downstream assets need full dataset
- Dataset size manageable after processing

### Pattern 4: Chained Partitioning

Multiple partition schemes in sequence:

```
load_attempts (partitionOut: attempt_id)
    ↓
trfm_by_form (repartitionOut: form_code, categorical)
    ↓
trfm_by_school (repartitionOut: school_id, categorical)
```

**Use When:**
- Need different groupings at different stages
- Each stage benefits from different partition scheme

## Best Practices

### 1. Partition Early

**Do:**
```typescript
// Partition immediately after loading
{
  slug: "load_attempts",
  partitionOut: { key: "attempt_id", field: "attempt_id", partSize: 10000 }
}
```

**Don't:**
```typescript
// Wait until later to partition
{
  slug: "load_attempts"  // No partitioning
}
{
  slug: "trfm_later",
  partitionOut: { ... }  // Too late, already loaded into memory
}
```

### 2. Choose Appropriate Partition Size

**For Numeric Partitioning:**
- Start with 10,000-25,000 records
- Adjust based on:
  - Record size (larger records = smaller partitions)
  - Available memory
  - Processing complexity
  - Dataset size

**For Categorical Partitioning:**
- Use when categories represent natural boundaries
- Ensure reasonable number of categories (<1000)
- Consider category size distribution

### 3. Match Partition Keys

**Do:**
```typescript
// Upstream
{ partitionOut: { key: "attempt_id", ... } }

// Downstream
{ partitionBy: { key: "attempt_id" } }  // Matches!
```

**Don't:**
```typescript
// Upstream
{ partitionOut: { key: "attempt_id", ... } }

// Downstream
{ partitionBy: { key: "student_id" } }  // Mismatch! Error!
```

### 4. Use concatOut Sparingly

**Do:**
```typescript
// Keep partitioned for downstream processing
{ partitionBy: { key: "attempt_id", concatOut: false } }
```

**Don't:**
```typescript
// Only concat when absolutely necessary
{ partitionBy: { key: "attempt_id", concatOut: true } }  // Only if needed!
```

### 5. Document Partition Strategy

Always document why partitioning was chosen:

```typescript
{
  slug: "load_attempts",
  description: "Loads test attempts partitioned by attempt_id (10k chunks) to manage memory for large test windows",
  partitionOut: {
    key: "attempt_id",
    field: "attempt_id",
    partSize: 10000
  }
}
```

### 6. Monitor Partition Sizes

Check partition sizes after execution:
- Very small partitions (<100 records): Consider larger partSize
- Very large partitions (>100k records): Consider smaller partSize
- Uneven partition sizes: May indicate data distribution issues

### 7. Test with Production-Sized Data

Always test partitioning with realistic data volumes:
- Small test datasets may not reveal memory issues
- Partition size that works for 100k records may fail for 10M records
- Validate memory usage under load

### 8. Choose Partition Field Based on Downstream Multiplication

**The "UID vs. ID" Rule**: When data multiplies downstream (e.g., responses per attempt), partition by the higher-level identifier.

**Example (Newfoundland/Atlantic):**
- Partitioning by **UID** is preferred over **test_attempt_id** because:
  - One test attempt may have 50+ responses
  - Downstream logic often requires the full student chain
  - Partitioning by UID keeps all student-related data together within partitions

**When to Use Higher-Level Identifiers:**
- When one record type expands into many (attempt → responses)
- When downstream aggregations need all related records together
- When grouping operations require records from the same entity

## Troubleshooting

### Issue: Partition Key Mismatch - Partition Does Not Exist

**Error:**
```
partition ${partKey} does not exist
```

**Cause:** Downstream asset's `partitionBy.key` doesn't match upstream `partitionOut.key`

**Solution:** Ensure keys match exactly:
```typescript
// Upstream
partitionOut: { key: "attempt_id", ... }

// Downstream
partitionBy: { key: "attempt_id" }  // Must match!
```

### Issue: Using partitionBy on Query Assets (No Upstream Partitions)

**Error:**
```
partition ${partKey} does not exist
```

**Cause:** Using `partitionBy` on a `query` method asset that has no upstream partitions. The `partitionBy` configuration is only valid for `transform` method assets that process data from upstream partitioned assets.

**Common Mistake:**
```typescript
// ❌ WRONG - Query assets cannot use partitionBy
{
  slug: "load_user_roles",
  method: "query",  // Query method - no upstream data
  methodConfig: {
    querySlug: "SQL_USER_ROLES"
  },
  partitionBy: {  // ❌ ERROR: No upstream partition exists
    key: "test_window_id",
    concatOut: true
  }
}
```

**Solution:** 
- **If you want partitioned output**: Use `partitionOut` instead of `partitionBy`:
  ```typescript
  {
    slug: "load_user_roles",
    method: "query",
    partitionOut: {  // ✅ Correct - creates new partitions
      key: "test_window_id",
      field: "test_window_id",
      categorical: true
    }
  }
  ```
- **If you don't need partitioning**: Remove the partition configuration entirely:
  ```typescript
  {
    slug: "load_user_roles",
    method: "query",
    // No partition configuration - outputs single file
  }
  ```

**Code Review Checklist:**
- ✅ Verify that `partitionBy` is only used on `transform` method assets
- ✅ Verify that `partitionBy.key` matches an upstream asset's `partitionOut.key`
- ✅ Verify that query assets use `partitionOut` (if partitioning needed) or no partition config (if not needed)
- ✅ Remove `partition_date` or other partition-related columns from SQL queries when not using partitioning

### Issue: Memory Still High

**Symptoms:** Memory usage still high despite partitioning

**Causes:**
1. Partition size too large
2. Not partitioning early enough
3. Multiple large assets in memory simultaneously
4. Caching too many partitions

**Solutions:**
- Reduce `partSize` (try 5,000 instead of 10,000)
- Partition earlier in pipeline
- Set `storeCache: false` for assets not needed by multiple downstream assets
- Use `partitionBy` to avoid loading all partitions

### Issue: Too Many Small Partitions

**Symptoms:** Hundreds or thousands of tiny partitions

**Causes:**
- `partSize` too small
- Categorical partitioning with too many categories

**Solutions:**
- Increase `partSize` for numeric partitioning
- Consider numeric partitioning instead of categorical if categories are too numerous
- Combine small categories if possible

### Issue: Uneven Partition Sizes

**Symptoms:** Some partitions much larger than others

**Causes:**
- Data distribution not uniform
- Categorical partitioning with uneven category sizes

**Solutions:**
- For numeric: This is normal if data distribution is uneven
- For categorical: Consider if this is acceptable (may be natural)
- If problematic, consider different partition field

### Issue: Performance Degradation

**Symptoms:** Partitioned assets slower than expected

**Causes:**
1. Too many partitions (overhead)
2. Small partitions (overhead per partition)
3. Frequent S3 I/O

**Solutions:**
- Adjust partition size to balance memory vs. performance
- Consider fewer, larger partitions if memory allows
- Monitor S3 I/O patterns

## Partitioning Decision Tree

```
Is dataset >100k records?
├─ No → Don't partition (unless complex transforms)
└─ Yes → Continue

Is field sequential numeric (ID, timestamp)?
├─ Yes → Use numeric partitioning (partSize: 10k-25k)
└─ No → Continue

Is field categorical with natural boundaries?
├─ Yes → Use categorical partitioning
└─ No → Consider numeric partitioning on ID field

Will downstream assets need full dataset?
├─ Yes → Use concatOut: true at final step
└─ No → Keep partitioned throughout pipeline
```

## Related Documentation

- [Examples](./partitioning-examples.md)
- [Storage Operations](../storage/index.md) - Partitioning implementation details
- [Asset Model](../models/assets.md) - Asset partitioning configuration
- [Operational APIs](../operational-apis.md) - Memory management and risk mitigation
- [Data Export Service](../services/data-export.md) - Job execution with partitioning
