# Data Export Queries

This section documents all available data export queries in the VEA Data Lakehouse system. Queries are organized by category and provide detailed schema definitions for the fields they expose.

## Query Categories

- [00a Metadata Queries](./q00a_meta/index.md): Codebook and metadata queries
- [00b System Queries](./q00b_sys/index.md): System-level queries (test windows, etc.)
- [01a Assessment Specifications](./q01a_asmt_specs/index.md): Assessment specification queries
- [01b Assessment Content](./q01b_asmt_content/index.md): Assessment content queries (items, forms, designs)
- [02a Groups](./q02a_groups/index.md): School and group queries
- [02b Registrations](./q02b_registrations/index.md): Student and registration queries
- [03a Attempts](./q03a_attempts/index.md): Test attempt queries
- [03b Responses](./q03b_responses/index.md): Student response queries
- [03c Scans](./q03c_scans/index.md): Scan-related queries
- [04 Marking](./q04_marking/index.md): Marking window and scoring queries

## Query Structure

Each query definition includes:
- **Query Name**: The unique identifier for the query
- **Description**: What the query does
- **Required Inputs**: Parameters that must be provided
- **Optional Inputs**: Parameters that may be provided
- **Output Schema**: Field definitions with types and descriptions

## Usage

Queries are used within the DAG system as part of asset definitions. They fetch data from the database and produce structured outputs that can be used by other assets in the pipeline.

