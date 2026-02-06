# Data Lakehouse DAG Configuration Review Checklist

## Overview

This checklist ensures that Data Lakehouse DAG assets, queries, and transforms are correctly configured and follow best practices. Use this checklist when creating or modifying asset and job definitions.

## Query Assets Checklist

### Required Inputs Validation

- [ ] **All `requiredInputs` in query definition have corresponding `dependencySourcings` entries in the asset**
  - Check that every parameter listed in `requiredInputs` has a matching `dependencySourcings` entry
  - Missing required inputs will cause runtime errors: "Undefined binding(s) detected for keys [param_name]"
  - Example error: `Error: Undefined binding(s) detected for keys [role_types] when compiling RAW query`

- [ ] **All `dependencySourcings` entries match query `requiredInputs` or `optionalInputs`**
  - Extra dependency sourcings that don't match query inputs will generate warnings
  - Missing required inputs will cause runtime errors

- [ ] **Optional inputs are properly configured**
  - Optional inputs should be listed in `optionalInputs`, not `requiredInputs`
  - Verify that the underlying query supports optional parameters (check with query maintainer if unsure)

### Asset Configuration Validation

- [ ] **Asset schema matches query output columns**
  - All columns returned by the query should be defined in the asset schema
  - Column names (slugs) must match between query output and schema definition
  - Column types should match expected data types

- [ ] **Dependency sourcings are correctly configured** (This is the **authoritative** source for all dependencies)
  - `dependencySourcings` defines ALL dependencies - do NOT use `_dependencies` field (it's redundant)
  - Each `dependencySourcings` entry defines:
    - `param`: The parameter name that the asset/method needs
    - `type`: How to source the parameter (`job-config`, `asset-col`, or `dataframe`)
    - `config`: Configuration specific to the sourcing type (e.g., `config.asset` for dataframe sourcing)
  - For `job-config` sourcing: Parameter exists in pipeline configuration
  - For `asset-col` sourcing: Source asset exists and column exists
  - For `dataframe` sourcing: Source asset exists and is accessible (the `config.asset` field defines the dependency)
  - The system automatically infers the dependency graph from `dependencySourcings` entries where `config.asset` is specified

- [ ] **Asset slugs are verified to exist before use**
  - Every `config.asset` value must match an actual asset `slug` in `assets.ts`
  - Use `grep` to verify: `grep -n "slug.*asset_name" assets.ts`
  - Common typos: extra letters, wrong word order, underscore placement
  - Missing assets cause error: "Missing assets for job definition"

## Transform Assets Checklist

- [ ] **All transform `kind` values are valid**
  - Only use transforms that exist in `data-export-queries/services/_index.ts`
  - Valid kinds: `join`, `filter-col`, `drop-duplicates`, `group-by`, `map-col`, `restrict-cols`, `sort-by`, `aggregate`, `normed-histogram`, `replace-where`, `rename-cols`, `concat`, `fill-na`, `cutoffs`, `apply-cutscores`, `set-where`, `point-biserial`, `pivot`, `wr-stats`, `cast-col`, `overall-plus-one`, `priority-all-or-nothing`, `apply-disc-rules`, `extract-cols`, `round-number`
  - Do NOT use fake kinds like `placeholder`, `passthrough`, `copy`, `identity` - they don't exist
  - Invalid kinds cause error: "Transform [kind] does not exist in step [n]"

- [ ] **Transform dependencies are correctly configured via `dependencySourcings`**
  - **Do NOT use `_dependencies`** - it is redundant and should not be included
  - All upstream assets are defined in `dependencySourcings` entries where `config.asset` is specified
  - Each dataframe dependency should have a `dependencySourcings` entry with `type: "dataframe"` and `config.asset` pointing to the upstream asset
  - Dependency asset slugs in `config.asset` must match actual asset definitions
  - Column references in transforms match source asset schemas

- [ ] **Transform output schema matches expected output**
  - All output columns are defined in the schema
  - Column types match transform output types

- [ ] **Joins use natural keys, not cross-join workarounds**
  - Check if a foreign key relationship exists between the datasets being joined
  - Do NOT use dummy constant keys (`_cross_join_key = 1`) when a real join key exists
  - Ask: "What is the relationship between these entities? Is there a shared ID?"
  - See: [Anti-Pattern: Cross-Join When Natural Join Key Exists](#anti-pattern-cross-join-when-natural-join-key-exists)

## Common Errors to Avoid

### Error: Missing assets for job definition

**Cause:** Asset slug in job definition or `dependencySourcings.config.asset` doesn't match any actual asset slug.

**Example Error:**
```json
{
    "name": "Unprocessable",
    "message": "Missing assets for job definition",
    "code": 422,
    "data": {
        "missingAssets": ["load_twtdar"]
    }
}
```

**Fix:**
1. **Verify asset slug exists** before adding to job or dependency sourcing
2. Use exact slug from asset definition (case-sensitive, underscore placement matters)
3. Common mistakes:
   - `load_twtdar` vs `load_twtar` (extra 'd')
   - `load_ta_from_tw_raw` vs `load_ta_raw_from_tw` (word order)
   - Typos in asset names

**Prevention:**
```bash
# Search for correct asset slug before use
grep -n "slug.*load_twt" assets.ts
```

**Example:**
```typescript
// ❌ WRONG - asset slug typo
"tc-marking-validation": {
    assets: [
        "load_twtdar",  // Asset doesn't exist! Should be load_twtar
    ]
}

// ✅ CORRECT - use exact slug from assets.ts
"tc-marking-validation": {
    assets: [
        "load_twtar",   // Matches: slug: 'load_twtar' in assets.ts
    ]
}

// ❌ WRONG in dependencySourcings
dependencySourcings: [{
    param: "twtdar",
    config: {
        asset: "load_twtdar"  // Wrong slug!
    }
}]

// ✅ CORRECT
dependencySourcings: [{
    param: "twtdar",
    config: {
        asset: "load_twtar"  // Matches actual asset
    }
}]
```

### Error: Transform [kind] does not exist in step [n]

**Cause:** Using an invalid or non-existent transform kind in the `methodConfig.sequence`.

**Example Error:**
```json
{
    "name": "Unprocessable",
    "message": "Errors found in asset configuration",
    "data": {
        "errors": [
            "Asset trfm_validation_checks :: Transform placeholder does not exist in step 1"
        ]
    }
}
```

**Fix:**
1. **Only use valid transform kinds** from the registered list
2. Replace fake/placeholder transforms with real transforms
3. If you need to pass data through unchanged, use `restrict-cols` with all columns

**Valid Transform Kinds:**
```
join, filter-col, drop-duplicates, group-by, map-col, restrict-cols, sort-by,
aggregate, normed-histogram, replace-where, rename-cols, concat, fill-na,
cutoffs, apply-cutscores, set-where, point-biserial, pivot, wr-stats,
cast-col, overall-plus-one, priority-all-or-nothing, apply-disc-rules,
extract-cols, round-number
```

**Example:**
```typescript
// ❌ WRONG - 'placeholder' is not a valid transform
{
    kind: "placeholder",  // DOES NOT EXIST!
    df_output: "result",
    config: { df_input: "source_df" }
}

// ✅ CORRECT - use a real transform
{
    kind: "restrict-cols",
    df_output: "result",
    config: {
        df_input: "source_df",
        cols: ["col1", "col2", "col3"]
    }
}
```

**Prevention:**
- Check `data-export-queries/services/_index.ts` for the registered transform list
- See [Transforms Index](../transforms/index.md) for complete documentation

---

### Error: Undefined binding(s) detected

**Cause:** Required input parameter not provided in `dependencySourcings`

**Fix:**
1. Check the query definition's `requiredInputs` array (in the query asset definition)
2. Ensure all required inputs have corresponding `dependencySourcings` entries in your asset configuration
3. If the parameter is not always needed, verify with the query maintainer if it can be moved to `optionalInputs` in the query definition

**Example:**
```typescript
// ❌ WRONG - role_types is required by query but not in dependencySourcings
// Query definition has: requiredInputs: ['uids', 'role_types']
dependencySourcings: [
  { param: "uids", type: "job-config", ... }  // Missing role_types!
]

// ✅ CORRECT - Add dependency sourcing for all required inputs
dependencySourcings: [
  { param: "uids", type: "job-config", ... },
  { param: "role_types", type: "job-config", ... }
]
```

### Error: Parameter defined but not used in query

**Cause:** Parameter in `dependencySourcings` doesn't match any query input (neither `requiredInputs` nor `optionalInputs`)

**Fix:** Remove unused parameter from `dependencySourcings` or verify the parameter name matches the query definition

### Error: Required input not defined for query

**Cause:** Query `requiredInputs` entry missing from `dependencySourcings`

**Fix:** Add missing parameter to `dependencySourcings` or move to `optionalInputs`

### Anti-Pattern: Cross-Join When Natural Join Key Exists

**Cause:** Using a dummy constant key to perform a cross-join when the data model actually has a natural foreign key relationship that should be used instead.

**Example of the Anti-Pattern:**
```typescript
// ❌ WRONG - Cross-join via dummy key when natural join exists
{
  kind: "map-col",
  config: {
    df_input: "registrations",
    operation: "constant",
    value: 1,
    col_output: "_cross_join_key"
  },
  df_output: "registrations_with_key"
},
{
  kind: "map-col",
  config: {
    df_input: "sub_window_codes",
    operation: "constant",
    value: 1,
    col_output: "_cross_join_key"
  },
  df_output: "windows_with_key"
},
{
  kind: "join",
  config: {
    how: "inner",
    left: "registrations_with_key",
    right: "windows_with_key",
    left_on: ["_cross_join_key"],
    right_on: ["_cross_join_key"]
  },
  df_output: "result"  // Every registration × every window code!
}
```

**Why This Is Wrong:**
- Cross-joins create a Cartesian product (every row in A × every row in B)
- This produces incorrect relationships when the data model has a proper foreign key
- Causes massive data explosion (N × M rows instead of N rows)
- Results are semantically incorrect - each registration gets ALL window codes, not just the ones for its test window

**Correct Approach:**
```typescript
// ✅ CORRECT - Join on the natural foreign key relationship
{
  kind: "join",
  config: {
    how: "left",
    left: "registrations",       // Has sc_ss_tw_id (test window ID)
    right: "sub_window_codes",   // Has test_window_id
    left_on: ["sc_ss_tw_id"],
    right_on: ["test_window_id"]
  },
  df_output: "result"  // Each registration gets only its window codes
}
```

**Questions to Ask Before Using Cross-Join:**

1. **"What is the relationship between these two datasets?"**
   - One-to-many? Many-to-many? Or truly independent (no relationship)?
   - Cross-join is ONLY appropriate when there is NO logical relationship

2. **"Is there a shared key or foreign key in the data model?"**
   - Check schema for fields like `*_id`, `*_tw_id`, or similar linking fields
   - Look at upstream assets to see what keys are available

3. **"What should the output cardinality be?"**
   - If each row on the left should get ONE matching set of rows from the right (not ALL rows), you need a proper join key

4. **"Does an upstream asset already provide the linking key?"**
   - Often the join key exists but comes from a different table in the join chain
   - Example: `sc_ss_tw_id` comes from school groupings, not registrations directly

**Real-World Example:**

> Student registrations need window_code for their test window.
> - `load_db_school_groupings` has `sc_ss_tw_id` (the test window ID for each grouping)
> - `trfm_tw_sub_window_codes_from_twtar` has `test_window_id` and `window_code`
> - **Wrong:** Cross-join gives every student every window code
> - **Right:** Join on `sc_ss_tw_id = test_window_id` gives each student only their window's codes

**Prevention:**
- Always examine the schema of upstream assets for potential join keys
- Ask "How are these entities related in the business domain?"
- When unsure, draw out the data model relationships before configuring the transform

---

## Testing Checklist

- [ ] **Asset executes successfully with all required inputs provided**
- [ ] **Asset executes successfully with optional inputs omitted**
- [ ] **Asset schema validation passes**
- [ ] **Dependency validation passes (no missing dependencies)**

## Asset Reuse Validation

- [ ] **New asset creation is justified**
  - Configuration has searched for existing similar assets
  - No existing asset can be adapted with different dependency sourcings
  - Requirement cannot be met with existing assets + transforms
  - See: [Query Reuse Guidelines](./query-reuse-guidelines.md) for detailed process

- [ ] **No JSON columns in schema (unless truly necessary)**
  - JSON columns do not display in lakehouse views
  - Use flat columns instead (e.g., `created_by_uid`, `revoked_by_uid` instead of `role_metadata` JSON)
  - JSON is only acceptable for internal processing that gets extracted via transforms

## Configuration Review Process

1. **Before Review:**
   - Run validation checks (if available)
   - Check for TypeScript compilation errors in asset definitions
   - Verify all asset slugs referenced exist
   - Check if new asset is necessary (see Asset Reuse Validation)

2. **During Review:**
   - Use this checklist to verify configuration
   - Check for common errors listed above
   - Verify schema matches expected output
   - Verify dependency sourcings are complete and correct
   - Verify asset reuse guidelines are followed
   - Check for JSON columns that should be flattened

3. **After Review:**
   - Test asset execution if possible
   - Verify no runtime errors occur
   - Confirm output schema matches expectations

