# Jobs Model

## Source File
`api--abed/src/services/public/data-exporter/data-export/model/jobs.ts`

## Overview

Jobs are collections of assets that work together to accomplish a specific data export or processing task. Jobs define which assets to execute, their configuration, and execution scope.

## DagJobDef Interface

```typescript
export interface DagJobDef {
  assets: string[];                              // List of asset slugs to execute
  scope: string;                                 // Execution scope
  pipeline_config: {                             // Default pipeline configuration
    [key: string]: boolean | number | string | number[] | string[]
  };
  pipeline_config_req: {                         // Required configuration parameters
    [key: string]: string                        // Parameter name -> type
  };
  pipeline_config_opt?: {                        // Optional configuration parameters
    [key: string]: string                        // Parameter name -> type
  };
  description?: string;                          // Job description
  isDisabled?: boolean;                          // Whether job is disabled
}
```

## Job Scopes

Jobs can be scoped to different contexts:

- `schools`: School-based assessments
- `test-centres`: Test centre-based assessments

## Pipeline Configuration

### Required Parameters

Parameters that must be provided when creating a job:

```typescript
pipeline_config_req: {
  "test_window_ids": "number[]"
}
```

### Optional Parameters

Parameters that can be optionally provided:

```typescript
pipeline_config_opt: {
  "twtar_type_slugs": "string[]",
  "include_sample_assessments": "boolean"
}
```

### Default Configuration

Default values for configuration parameters:

```typescript
pipeline_config: {
  "include_sample_assessments": false,
  "include_questionnaire": true,
  "only_secured_twtdar": false,
  "include_sample_schools": false
}
```

## Example Job Definition

```typescript
"tc-attempts-school": {
  "description": "Test controller tables for school-based assessments",
  "assets": [
    "load_ta_detail",
    "load_ts_detail",
    "trfm_attempts_by_form_code",
  ],
  "scope": "schools",
  "pipeline_config": {
    "include_sample_assessments": false,
    "include_questionnaire": true,
    "only_secured_twtdar": false,
  },
  "pipeline_config_req": {
    "test_window_ids": "number[]",
  }
}
```

## Common Pipeline Configuration Parameters

### Test Window Parameters

- `test_window_ids` (number[]): List of test window IDs to process
- `twtar_type_slugs` (string[]): Filter by test window TD allocation rule types

### Filtering Parameters

- `include_sample_assessments` (boolean): Include sample assessments
- `include_questionnaire` (boolean): Include questionnaire data
- `only_secured_twtdar` (boolean): Only include secured test window TD allocation rules
- `include_sample_schools` (boolean): Include sample schools
- `include_sample_students` (boolean): Include sample students
- `include_not_reported_schools` (boolean): Include schools that haven't reported

### Marking Parameters

- `marking_window_id` (number): Marking window ID for marking-related jobs
- `only_human_marked_twtdar` (boolean): Only include human-marked assessments

### District/School Parameters

- `schl_dist_group_ids` (number[]): Filter by school district group IDs
- `schl_group_ids` (number[]): Filter by school group IDs
- `schl_class_group_ids` (number[]): Filter by class group IDs
- `uids` (number[]): Filter by user IDs

### EYS-Specific Parameters

- `sub_window_codes` (string[]): Filter by sub-window codes
- `ignore_PASI_grade_check` (boolean): Ignore PASI grade validation

### Delta/Comparison Parameters

- `export_ids` (number[]): Export IDs to compare (for delta jobs)
- `asset_slugs` (string[]): Specific assets to include in comparison

## Job Execution Flow

1. **Job Creation**: Client creates job with configuration
2. **Asset Resolution**: System resolves all assets in `assets` array
3. **Dependency Resolution**: System builds dependency graph
4. **Validation**: Validates required parameters are provided
5. **Execution**: Assets executed in topological order
6. **Storage**: Results stored in S3
7. **Packaging**: Final assets packaged for download
8. **Completion**: Job status updated

## Job Registration

Jobs are registered in `TEMP_DAG_JOBS` object in the jobs.ts file. Each job is keyed by its slug.

## Common Job Patterns

### 1. Simple Data Export

Export a single dataset:

```typescript
"item-register": {
  "assets": ["load_db_td", "load_db_form_designs", "trfm_item_register_consolidated"],
  "scope": "schools",
  "pipeline_config": {
    "include_sample_assessments": false
  },
  "pipeline_config_req": {
    "test_window_ids": "number[]"
  }
}
```

### 2. Multi-Asset Export

Export multiple related datasets:

```typescript
"tc-attempts-school": {
  "assets": [
    "load_ta_detail",
    "load_ts_detail",
    "trfm_attempts_by_form_code"
  ],
  "scope": "schools",
  "pipeline_config": {...},
  "pipeline_config_req": {
    "test_window_ids": "number[]"
  }
}
```

### 3. Validation/Check Job

Job focused on data validation:

```typescript
"tw-marking-pool-readiness": {
  "assets": [
    "load_db_marking_pooled_attempts_by_asmt_code",
    "load_db_marking_not_pooled_attempts"
  ],
  "scope": "schools",
  "pipeline_config": {
    "only_human_marked_twtdar": true
  },
  "pipeline_config_req": {
    "test_window_ids": "number[]"
  }
}
```

### 4. Delta/Comparison Job

Compare two exports to identify data changes (inserts, removals, and updates):

**Note**: Delta jobs are now available in the abed DAG. The `delta-lite` job compares two export runs and generates detailed change reports.

```typescript
"delta-lite": {
  "description": "Compare two exports to identify data changes",
  "assets": [
    "trfm_delta_detail",
    "trfm_delta_insert_removal",
    "trfm_delta_updates",
    "trfm_delta_summary"
  ],
  "scope": "schools",
  "pipeline_config": {},
  "pipeline_config_req": {
    "export_ids": "number[]"  // Must contain exactly 2 export IDs
  },
  "pipeline_config_opt": {
    "asset_slugs": "string[]"  // Optional: specific assets to compare
  }
}
```

**Delta Job Details**:
- **Purpose**: Compare two completed export runs to identify differences in asset data
- **Required Parameters**: `export_ids` array must contain exactly 2 export IDs (reference and comparison)
- **Optional Parameters**: `asset_slugs` to limit comparison to specific assets
- **Output Assets**:
  - `trfm_delta_detail`: Detailed change records (inserts, removals, updates)
  - `trfm_delta_insert_removal`: Summary of inserts and removals by asset
  - `trfm_delta_updates`: Summary of field-level updates
  - `trfm_delta_summary`: Aggregated summary of all changes
- **Comparison Logic**: Uses primary key fields from asset schemas to match records, then compares non-primary-key fields for changes

## Best Practices

1. **Asset Ordering**: List assets in logical order (though execution order is determined by dependencies)

2. **Configuration**: Provide sensible defaults in `pipeline_config`

3. **Required Parameters**: Only mark parameters as required if they're truly necessary

4. **Documentation**: Include clear descriptions for each job

5. **Scope**: Always specify the appropriate scope

6. **Naming**: Use descriptive, consistent naming conventions

7. **Disabling**: Use `isDisabled: true` to temporarily disable jobs without deleting them

8. **Verify Asset Slugs**: Always verify that asset slugs in the `assets` array match actual asset definitions
   - Use `grep -n "slug.*asset_name" assets.ts` to find the correct slug
   - Common errors: typos, extra letters (e.g., `load_twtdar` vs `load_twtar`), wrong word order
   - Missing assets cause error: `"Missing assets for job definition"`

## Common Errors

### Missing Assets for Job Definition

```json
{
    "message": "Missing assets for job definition",
    "data": { "missingAssets": ["load_twtdar"] }
}
```

**Cause**: Asset slug in `assets` array doesn't match any actual asset `slug` in `assets.ts`.

**Fix**: 
1. Search for the correct asset slug: `grep -n "slug.*load_twt" assets.ts`
2. Update the job definition with the correct slug
3. Verify all asset references before committing

