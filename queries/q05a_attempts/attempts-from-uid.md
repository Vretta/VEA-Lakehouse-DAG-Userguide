# Attempts from UID

**Query Name:** `SQL_05A_ATTEMPTS_FROM_UID`

## Description

Retrieves test attempts for specified user IDs, including detailed attempt information.

## Required Inputs

- `uids` (number[]): Array of user IDs

## Optional Inputs

- `include_sample_assessments` (boolean): Include sample assessments (default: false)
- `sc_id` (number): Filter by school class ID
- `s_id` (number): Filter by school ID
- `sd_id` (number): Filter by school district ID
- `ta_ids` (number[]): Filter by test attempt IDs
- `twtar_ids` (number[]): Filter by test window TD allocation rule IDs
- `schl_dist_group_ids` (number[]): Filter by school district group IDs
- `eys_only` (boolean): EYS only flag (deprecated)
- `limit` (number): Maximum number of records to return (default: 500000)

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `attempt_id` | number | Test attempt ID |
| `uid` | number | User ID |
| `sc_id` | number | School class ID |
| `sc_name` | string | School class name |
| `sc_gid` | number | School class group ID |
| `s_id` | number | School ID |
| `s_name` | string | School name |
| `s_gid` | number | School group ID |
| `sd_id` | number | School district ID |
| `ts_id` | number | Test session ID |
| `lang` | string | Language code |
| `test_form_id` | number | Test form ID |
| `created_on` | timestamp | Creation timestamp |
| `started_on` | timestamp | Start timestamp |
| `is_closed` | boolean | Whether the attempt is closed |
| `closed_on` | timestamp | Close timestamp |
| `time_ext_m` | number | Time extension in minutes |
| `is_invalid` | boolean | Whether the attempt is invalid |
| `last_updated_by_uid` | number | Last updated by user ID |
| `twtar_id` | number | Test window TD allocation rule ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/attempts-from-uid.ts`

