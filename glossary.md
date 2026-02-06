# Glossary

This glossary defines key terms used throughout the VEA Data Lakehouse DAG documentation. Terms are organized by category for easier navigation.

## DAG Architecture Terms

### Asset
A fundamental building block of the Data Lakehouse DAG. Each asset represents a single data processing operation that produces a data output. Assets define how data is sourced, processed, and structured. See [Assets Model](./models/assets.md) for details.

### Check Asset
A special type of asset used for data validation. Check assets have a `severity` level and are used to identify data quality issues or inconsistencies in the pipeline.

### Dependency Sourcing
The mechanism by which assets obtain their required inputs. Dependencies can be sourced from:
- **Job Config**: Values from the job's pipeline configuration
- **Asset Column**: Extracted values from a column in another asset
- **Dataframe**: The entire dataframe from another asset

The `dependencySourcings` array is the authoritative source for all asset dependencies. See [Assets Model - Dependency Sourcing](./models/assets.md#dependency-sourcing) for details.

### Job
A collection of assets that work together to accomplish a specific data export or processing task. Jobs define which assets to execute, their configuration, and execution scope. See [Jobs Model](./models/jobs.md) for details.

### Method
The processing method used by an asset to produce its output. Common methods include:
- **query**: Executes a SQL query against the database
- **query-chunked**: Executes a query in chunks for large datasets
- **transform**: Applies a data transformation function
- **api-method**: Calls an internal API endpoint

### Method Config
Configuration specific to the asset's method. For example, a `query` method requires a `querySlug`, while a `query-chunked` method requires both a `querySlug` and `chunkSize`.

### Partitioning
A technique for managing memory usage and enabling processing of large datasets by breaking them into smaller, manageable chunks. See [Partitioning Guide](./guides/partitioning-guide.md) for details.

#### Numeric Partitioning
Partitions data by numeric ranges using a fixed partition size. Used for sequential IDs (attempt_id, student_id, etc.) or timestamps. Each partition contains records within a numeric range (e.g., attempt_id 0-9999, 10000-19999, etc.).

#### Categorical Partitioning
Partitions data by unique categorical values, creating one partition per category. Used for school-based data (partition by school_id), district-based data, or form-based data. Each partition contains all records for a specific categorical value.

#### Partition Out
Configuration used when an asset produces partitioned data. Defines the partition key, field, and size (for numeric) or categorical flag.

#### Partition By
Configuration used when an asset processes already-partitioned data from upstream assets. Must match the partition key from the upstream asset's `partitionOut`.

### Pipeline Config
Configuration parameters passed to a job that control its behavior. Parameters can be required (`pipeline_config_req`), optional (`pipeline_config_opt`), or have default values (`pipeline_config`). Common parameters include `test_window_ids`, `include_sample_assessments`, and filtering options.

### Query
A SQL query executed against the database to retrieve data. Queries are defined with required and optional inputs, and produce structured output according to a defined schema.

### Scope
The execution context for jobs and assets. Common scopes include:
- **schools**: School-based assessments
- **test-centres**: Test centre-based assessments

### Schema
The structure definition for an asset's output, specifying field names, types, constraints, and relationships. Schemas include field definitions with types, captions, descriptions, primary keys, foreign keys, and nullability.

### Slug
A unique identifier used throughout the system. Assets, jobs, queries, and transforms all use slugs as their primary identifiers. Slugs are typically lowercase with underscores (e.g., `load_ta_detail`, `trfm_attempt_scores`).

### Structure
The output structure type for an asset. Common structures include:
- **dataframe**: Tabular data structure
- **dataframe-chunked**: Chunked tabular data
- **zip**: Compressed archive
- **excel**: Excel workbook

### Transform
A data transformation function that processes input dataframes to produce output dataframes. Transforms can perform operations like filtering, grouping, merging, aggregating, and column manipulation.

## Assessment-Related Terms

### Form Code
A unique alphanumeric identifier (e.g., "A", "B", "1", "2") for a specific test form. The form code distinguishes different variations of the same test design that may have different item orders, item sets, or other structural variations.

### Marking Window
A time period during which test attempts are marked or scored. Marking windows are used to organize and track the marking process for assessments.

### Response
A student's answer to an assessment item. Responses are stored with the item identifier, the response value, and metadata about the response entry.

### School
An educational institution that participates in assessments. Schools can be organized into districts and groups, and have associated students, teachers, and administrators.

### Student
A learner who participates in assessments. Students are associated with schools, have unique identifiers (UID), and may have accommodations, exceptions, or other special considerations.

### Test Attempt
A single instance of a student taking an assessment. Test attempts track the student's progress, timing, submission status, and are linked to test sessions and test window TD allocation rules.

### Test Design
The blueprint for an assessment, defining items, modules, forms, and assessment structure. Test designs are allocated to test windows through test window TD allocation rules.

### Test Form
A specific instantiation of a test design, identified by a form code (e.g., "A", "B") and test_form_id. Test forms are variations of a test design that may have different item orders, item sets, or other structural variations while assessing the same content. Students are assigned to specific test forms when taking assessments, and test attempts reference the test_form_id.

### Test Session
A session during which a student takes a test. Test sessions track timing, progress, and state during the assessment. Multiple test sessions may be associated with a single test attempt.

### Test Window
A time period during which assessments are administered. Test windows define the start and end dates, configuration flags (QA, active, PASI sync), and are the primary organizational unit for assessment data exports.

### Test Window TD Allocation Rule (TWTAR)
A rule that allocates a test design (TD) to a test window. TWTARs define which forms are available in a test window, their security settings, marking requirements, and other allocation-specific configurations. TWTARs are identified by `twtar_id` and have type slugs that categorize them.

## Data Model Terms

### Column
A single field in a dataframe or database table. Columns have a name, data type, and may have constraints like primary key, foreign key, or nullability.

### Dataframe
A tabular data structure consisting of rows (records) and columns (fields). Dataframes are the primary data structure used throughout the DAG system for data processing and transformation.

### Field Definition
A specification for a column in an asset's schema, including:
- **slug**: Column identifier
- **type**: Data type (e.g., INTEGER_UNSIGNED, VARCHAR, BOOLEAN)
- **caption**: Human-readable name
- **description**: Column description
- **is_primary_key**: Whether this is a primary key
- **is_nullable**: Whether null values are allowed
- **fk_table_slug**: Foreign key table reference
- **fk_field_slug**: Foreign key field reference

### Foreign Key
A field that references a primary key in another table. Foreign keys establish relationships between tables and are used for joins and referential integrity.

### Partition Key
The identifier used to match partitions between upstream and downstream assets. The partition key in `partitionBy` must match the partition key in the upstream asset's `partitionOut`.

### Primary Key
A field or combination of fields that uniquely identifies each record in a table. Primary keys are used for record identification, joins, and delta comparisons.

### Record
A single row in a dataframe or database table. Records contain values for each column defined in the schema.

### UID (User ID)
A unique identifier for a user in the system, typically referring to a student, teacher, or administrator. UIDs are numeric and used throughout the system to reference users.

