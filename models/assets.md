# Assets Model

## Source File
`api--abed/src/services/public/data-exporter/data-export/model/assets.ts`

## Overview

Assets are the fundamental building blocks of the Data Lakehouse DAG. Each asset represents a single data processing operation that produces a data output.

## DagAsset Interface

```typescript
export interface DagAsset {
  slug: string;                    // Unique identifier for the asset
  scopes?: string[];               // Scopes this asset applies to (schools, test-centres)
  caption?: string;                // Human-readable caption
  description: string;             // Detailed description
  isCheckAsset?: boolean;          // Whether this is a validation/check asset
  severity?: string;               // Severity level for check assets
  structure: string;               // Output structure type
  method: string;                 // Processing method
  methodConfig: any;               // Method-specific configuration
  partitionOut?: {                 // Partitioning configuration for output
    key: string, 
    field: string, 
    partSize?: number, 
    categorical?: boolean
  };
  partitionBy?: {                  // Partitioning configuration for input
    key: string, 
    concatOut?: boolean
  };
  dependencySourcings: DependencySourcing[];  // How to source dependencies (AUTHORITATIVE - defines all dependencies)
  storeCache?: boolean;            // Whether to cache in memory
  schema?: FieldDef[];             // Output schema definition
}
```

## Field Definitions

```typescript
export interface FieldDef {
  slug: string,                    // Column identifier
  type: string,                    // Data type
  caption?: string,                // Human-readable name
  description?: string,            // Column description
  is_primary_key?: boolean,        // Whether this is a primary key
  is_nullable?: boolean,          // Whether null values are allowed
  fk_table_slug?: string,          // Foreign key table reference
  fk_field_slug?: string,          // Foreign key field reference
}
```

## Dependency Sourcing

**IMPORTANT**: The `dependencySourcings` array is the **authoritative source** for all asset dependencies. It defines:
1. **What parameters** the asset needs (via `param`)
2. **How to source** each parameter (via `type` and `config`)
3. **Which assets** this asset depends on (implicitly through `config.asset` references)

**Do NOT use `_dependencies`**: This field is redundant and should not be included in asset definitions. The dependency graph is automatically derived from `dependencySourcings` entries.

Assets can source dependencies from three types of sources:

### 1. Job Config Sourcing

Sources values from the job's pipeline configuration.

```typescript
{
  param: "tw_ids",
  type: "job-config",
  config: {
    configSlug: "pipeline_config",
    param: "test_window_ids"
  }
}
```

### 2. Asset Column Sourcing

Extracts values from a column in another asset.

```typescript
{
  param: "ta_ids",
  type: "asset-col",
  config: {
    asset: "load_ta_from_tw",
    col: "attempt_id"
  }
}
```

### 3. Dataframe Sourcing

Uses the entire dataframe from another asset.

```typescript
{
  param: "students_df",
  type: "dataframe",
  config: {
    asset: "load_db_students"
  }
}
```

## Validation and Code Review

When creating or modifying assets, ensure all query `requiredInputs` have corresponding `dependencySourcings` entries. Missing required inputs will cause runtime errors.

**See:** [Code Review Checklist](/architecture/docs/architecture/data_lakehouse_dag/guides/code-review-checklist.md) for comprehensive validation guidelines.

## Asset Methods

### Query Method

Executes a SQL query against the database.

```typescript
{
  method: "query",
  methodConfig: {
    querySlug: "SQL_05A_ATTEMPTS_FROM_TW_ID"
  }
}
```

### Query Chunked Method

Executes a query in chunks for large datasets.

```typescript
{
  method: "query-chunked",
  methodConfig: {
    querySlug: "SQL_05A_ATTEMPTS_DETAIL_FROM_TA_ID",
    chunkedParam: "ta_ids",
    chunkSize: 2500
  }
}
```

### Transform Method

Applies a data transformation function.

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "group-by",
    // Additional transform-specific config
  }
}
```

### API Method

Calls an internal API endpoint.

```typescript
{
  method: "api-method",
  methodConfig: {
    methodSlug: "domain-schema",
    // Additional API-specific config
  }
}
```

## Partitioning

### Partition Out

Partitions the output data by a field.

**Numeric Partitioning:**
```typescript
partitionOut: {
  key: "attempt_id",
  field: "attempt_id",
  partSize: 10000  // Partition every 10,000 IDs
}
```

**Categorical Partitioning:**
```typescript
partitionOut: {
  key: "school_id",
  field: "school_id",
  categorical: true  // One partition per unique school_id
}
```

### Partition By

Processes input data that is already partitioned.

```typescript
partitionBy: {
  key: "attempt_id",
  concatOut: true  // Concatenate all partitions into single output
}
```

## Asset Scopes

Assets can be scoped to different contexts:

- `schools`: School-based assessments
- `test-centres`: Test centre-based assessments

## Example Asset Definition

```typescript
{
  "slug": "load_ta_detail",
  "caption": "test_attempt_detail",
  "description": "Test attempts table for the test-controller view",
  "structure": "dataframe",
  "schema": [
    {
      "slug": "attempt_id",
      "type": "INTEGER_UNSIGNED",
      "caption": "Test Attempt Id",
      "description": "Identifier for the test attempt.",
      "is_primary_key": true
    },
    {
      "slug": "student_uid",
      "type": "INTEGER_UNSIGNED",
      "caption": "Student User Id",
      "description": "Foreign key referencing the users table.",
      "is_primary_key": false
    }
  ],
  "method": "query-chunked",
  "methodConfig": {
    "querySlug": "SQL_05A_ATTEMPTS_DETAIL_FROM_TA_ID",
    "chunkedParam": "ta_ids",
    "chunkSize": 2500
  },
  "dependencySourcings": [
    {
      "param": "ta_ids",
      "type": "asset-col",
      "config": {
        "asset": "load_ta_from_tw",
        "col": "attempt_id"
      }
    }
  ]
}
```

## Asset Registration

Assets are registered in `TEMP_DAG_ASSETS` array in the assets.ts file. The system automatically:
1. Builds dependency graphs
2. Validates dependencies
3. Resolves execution order
4. Manages data flow between assets

## Asset Versioning and Storage

### Database-Backed Asset Definitions

For all jurisdictions except ABED, asset definitions are versioned and stored in the database table `data_def_assets`. This provides:

- **Version Control**: Track changes to asset definitions over time
- **Audit Trail**: Record who created and modified assets
- **Invalidation Support**: Mark assets as invalid when needed
- **Preview Support**: Distinguish between preview and production assets

**Database Table**: `data_def_assets`

**Key Columns**:
- `id`: Primary key
- `slug`: Unique asset identifier (generated)
- `definition`: JSON column containing the full asset definition (INVISIBLE)
- `is_preview`: Whether this is a preview asset (default: 1)
- `is_invalid`: Whether this asset is invalid (default: 0)
- `created_by_uid`, `created_on`: Creation tracking
- `updated_on`: Last update timestamp
- `invalidated_by_uid`, `invalidated_on`: Invalidation tracking

**Note**: ABED jurisdiction currently uses code-based asset definitions in `assets.ts` files rather than database-backed definitions.

## Best Practices

1. **Naming**: Use descriptive slugs with prefixes:
   - `load_*` for data loading assets
   - `trfm_*` for transformation assets
   - `check_*` for validation assets

2. **Schema**: Always define schemas for documentation and validation
   - **Avoid JSON columns**: JSON columns do not display in lakehouse views. Use flat columns instead (e.g., `created_by_uid`, `revoked_by_uid` instead of `role_metadata` JSON)
   - JSON is only acceptable for internal processing that gets extracted via transforms

3. **Query Reuse**: **Always check for existing queries before creating new ones**
   - Search existing queries in `data-export-queries/queries/`
   - Extend existing queries with optional parameters when possible
   - Use transforms to modify query output rather than creating duplicate queries
   - See: [Query Reuse Guidelines](../guides/query-reuse-guidelines.md)

4. **Dependencies**: All dependencies are defined in `dependencySourcings`. **Do NOT** use `_dependencies` - it is redundant. The system automatically infers the dependency graph from `dependencySourcings` entries where `config.asset` is specified.

5. **Partitioning**: Use partitioning for large datasets to improve performance

6. **Scopes**: Specify scopes when assets are context-specific

7. **Lightweight Queries**: Prefer simple queries that return core data, then use transforms for joins and enrichment

8. **Verify Referenced Assets**: When using `config.asset` in dependency sourcings, verify the asset slug exists:
   - Use `grep -n "slug.*asset_name" assets.ts` to find the correct slug
   - Common typos: extra letters (e.g., `load_twtdar` vs `load_twtar`), wrong word order
   - Missing assets cause error: `"Missing assets for job definition"`

## Common Errors

### Missing Assets for Job Definition

```json
{
    "message": "Missing assets for job definition",
    "data": { "missingAssets": ["load_twtdar"] }
}
```

**Cause**: Asset slug in `dependencySourcings.config.asset` doesn't match any actual asset `slug`.

**Example:**
```typescript
// ❌ WRONG - asset slug typo
dependencySourcings: [{
    param: "twtdar",
    type: "dataframe",
    config: {
        asset: "load_twtdar"  // Wrong! Should be load_twtar
    }
}]

// ✅ CORRECT
dependencySourcings: [{
    param: "twtdar",
    type: "dataframe",
    config: {
        asset: "load_twtar"  // Matches actual asset: slug: 'load_twtar'
    }
}]
```

**Prevention**: Before adding an asset reference, verify it exists:
```bash
grep -n "slug.*load_twt" assets.ts
```

