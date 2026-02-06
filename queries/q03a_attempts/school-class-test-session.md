# School Class Test Session

**Query Name:** `SQL_05A_SCHOOL_CLASS_TEST_SESSION`

## Description

Retrieves school class test session mappings with school and district information.

## Required Inputs

- `sc_ids` (number[]): Array of school class IDs
- `twtar_type_slugs` (string[]): Array of test window TD allocation rule type slugs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `scts_id` | number | School class test session ID |
| `sc_id` | number | School class ID |
| `ts_id` | number | Test session ID |
| `twtar_type_slug` | string | Test window TD allocation rule type slug |
| `school_name` | string | School name |
| `school_code` | string | School code (foreign ID) |
| `school_authority_code` | string | School authority code (school district foreign ID) |
| `is_sample_school` | boolean | Whether this is a sample school |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/school-class-test-session.ts`

