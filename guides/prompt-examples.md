# Prompt Examples for Data Lakehouse DAG Development

## Overview

This guide provides prompt examples for developing and extending the Data Lakehouse DAG system. These prompts reference the architectural specifications in the parent directories.

## Related Guides

- [Partitioning Guide](./partitioning-guide.md) - Comprehensive guide to using partitioning effectively
- [Specification Reference](./specification-reference.md) - Quick reference to specification-only documentation

## Creating a New Transform Function

### Prompt Template

```
I need to create a new transform function for the Data Lakehouse DAG system. 

Reference the transform specifications in:
- architecture/docs/architecture/data_lakehouse_dag/transforms/

The new transform should:
- [Describe functionality]
- Accept parameters: [list parameters]
- Return: [describe output]

Follow the pattern established in transforms like [similar transform name].md

Implementation requirements:
1. Create the transform function in api--abed/src/services/public/data-exporter/data-export-queries/transforms/[transform-name].ts
2. Register it in services/transforms.ts
3. Add documentation following the transform specification format
4. Include type definitions and error handling
```

### Example

```
I need to create a new transform function called "normalize-scores" that normalizes 
scores to a 0-100 scale based on min/max values.

Reference: architecture/docs/architecture/data_lakehouse_dag/transforms/

The transform should:
- Accept a dataframe with score columns
- Accept min and max values (either static or from config)
- Normalize scores: (score - min) / (max - min) * 100
- Return dataframe with normalized scores

Follow the pattern in map-col.md and set-where.md for implementation.
```

## Creating a New Asset

### Prompt Template

```
I need to create a new asset for the Data Lakehouse DAG system.

Reference the asset specifications:
- architecture/docs/architecture/data_lakehouse_dag/models/assets.md
- architecture/docs/architecture/data_lakehouse_dag/models/types.md

Asset requirements:
- Slug: [asset-slug]
- Description: [description]
- Method: [query|transform|api-method]
- Dependencies: [list dependencies]
- Schema: [list output columns]

The asset should be added to:
api--abed/src/services/public/data-exporter/data-export/model/assets.ts

Follow the asset definition pattern shown in assets.md.
```

### Example

```
Create a new asset "load_student_enrollments" that loads student enrollment data.

Reference: architecture/docs/architecture/data_lakehouse_dag/models/assets.md

Requirements:
- Slug: load_student_enrollments
- Method: query
- Query slug: SQL_STUDENT_ENROLLMENTS
- Dependencies: test_window_ids from job-config
- Schema: student_id, enrollment_date, school_id, grade, status

Add to TEMP_DAG_ASSETS array following the pattern in assets.md.
```

## Creating a New Job

### Prompt Template

```
I need to create a new export job for the Data Lakehouse DAG system.

Reference:
- architecture/docs/architecture/data_lakehouse_dag/models/jobs.md
- architecture/docs/architecture/data_lakehouse_dag/models/assets.md

Job requirements:
- Job name: [job-name]
- Scope: [schools|test-centres]
- Assets: [list asset slugs]
- Required config: [list required parameters]
- Optional config: [list optional parameters]
- Default config: [list defaults]

Add to TEMP_DAG_JOBS in:
api--abed/src/services/public/data-exporter/data-export/model/jobs.ts
```

### Example

```
Create a new job "student-performance-report" that exports student performance data.

Reference: architecture/docs/architecture/data_lakehouse_dag/models/jobs.md

Requirements:
- Job name: student-performance-report
- Scope: schools
- Assets: load_students, load_attempts, trfm_student_scores, trfm_performance_summary
- Required: test_window_ids (number[])
- Optional: include_sample_schools (boolean)
- Defaults: include_sample_schools: false

Follow the job definition pattern in jobs.md.
```

## Modifying an Existing Transform

### Prompt Template

```
I need to modify the [transform-name] transform function.

Reference:
- architecture/docs/architecture/data_lakehouse_dag/transforms/[transform-name].md
- Source: api--abed/src/services/public/data-exporter/data-export-queries/transforms/[transform-name].ts

Changes needed:
- [Describe changes]
- [List affected parameters]
- [Describe new behavior]

Ensure backward compatibility and update documentation.
```

## Debugging a Job Execution

### Prompt Template

```
I'm debugging a failing export job. Reference the architecture:

- architecture/docs/architecture/data_lakehouse_dag/services/data-export.md
- architecture/docs/architecture/data_lakehouse_dag/storage/index.md

Job details:
- Job name: [job-name]
- Export ID: [id]
- Error: [error message]
- Failed asset: [asset-slug if known]

Help me trace through:
1. Job configuration validation
2. Asset dependency resolution
3. Execution flow
4. Storage operations
```

## Adding Partitioning to an Asset

### Prompt Template

```
I need to add partitioning to an existing asset.

Reference:
- architecture/docs/architecture/data_lakehouse_dag/guides/partitioning-guide.md (comprehensive partitioning guide)
- architecture/docs/architecture/data_lakehouse_dag/models/assets.md (partitionOut, partitionBy)
- architecture/docs/architecture/data_lakehouse_dag/storage/index.md (partitioning functions)

Asset: [asset-slug]
Partition type: [numeric|categorical]
Partition field: [field-name]
Partition size: [size if numeric]

Follow the patterns in partitioning-guide.md and ensure downstream assets handle partitions correctly.
```

## Integration with Web Client

### Prompt Template

```
I need to integrate a new export job with the web client.

Reference:
- architecture/docs/architecture/data_lakehouse_dag/web-client/index.md

Requirements:
- Job name: [job-name]
- UI component: [component-name]
- User inputs: [list inputs]
- Display outputs: [list outputs]

Create the integration following the patterns in web-client/index.md.
```

## Best Practices Prompts

### Code Review Prompt

```
Review this Data Lakehouse DAG code against the architecture:

Reference specifications:
- architecture/docs/architecture/data_lakehouse_dag/

Check for:
1. Adherence to type definitions in models/types.md
2. Proper error handling
3. Correct dependency sourcing patterns
4. Schema definitions for assets
5. Configuration validation
```

### Performance Optimization Prompt

```
Optimize this Data Lakehouse DAG job for performance.

Reference:
- architecture/docs/architecture/data_lakehouse_dag/storage/index.md (caching, partitioning)
- architecture/docs/architecture/data_lakehouse_dag/services/data-export.md (execution flow)

Current job: [job-name]
Issues: [performance issues]

Suggest optimizations for:
1. Asset execution order
2. Partitioning strategy
3. Caching strategy
4. Query optimization
```

## Quick Reference

When working with the Data Lakehouse DAG, always reference:

1. **Models**: `architecture/docs/architecture/data_lakehouse_dag/models/`
   - Types, assets, jobs

2. **Transforms**: `architecture/docs/architecture/data_lakehouse_dag/transforms/`
   - One file per transform function

3. **Services**: `architecture/docs/architecture/data_lakehouse_dag/services/`
   - API service implementations

4. **Storage**: `architecture/docs/architecture/data_lakehouse_dag/storage/`
   - S3 operations, partitioning

5. **Web Client**: `architecture/docs/architecture/data_lakehouse_dag/web-client/`
   - Client integration patterns

