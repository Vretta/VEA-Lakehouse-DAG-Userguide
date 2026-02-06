# Attempts from Test Window ID

**Query Name:** `SQL_05A_ATTEMPTS_FROM_TW_ID`

## Description

Retrieves test attempts for specified test windows, including school, class, and session information.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

- `include_sample_assessments` (boolean): Include sample assessments (default: false)
- `restrict_to_submitted_attempts` (boolean): Only return submitted attempts (default: false)
- `sc_id` (number): Filter by school class ID
- `s_id` (number): Filter by school ID
- `sd_id` (number): Filter by school district ID
- `ta_ids` (number[]): Filter by test attempt IDs
- `uids` (number[]): Filter by user IDs
- `twtar_ids` (number[]): Filter by test window TD allocation rule IDs
- `schl_dist_group_ids` (number[]): Filter by school district group IDs
- `eys_only` (boolean): EYS only flag (deprecated)
- `limit` (number): Maximum number of records to return (default: 500000)

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `attempt_id` | number | Test attempt ID |
| `ta_is_closed` | boolean | Whether the attempt is closed |
| `uid` | number | User ID |
| `sc_id` | number | School class ID |
| `sc_name` | string | School class name |
| `sc_gid` | number | School class group ID |
| `s_id` | number | School ID |
| `s_name` | string | School name |
| `s_gid` | number | School group ID |
| `sd_id` | number | School district ID |
| `ts_id` | number | Test session ID |
| `ts_is_closed` | boolean | Whether the test session is closed |
| `twtar_id` | number | Test window TD allocation rule ID |
| `test_window_id` | number | Test window ID |
| `test_form_id` | number | Test form ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/attempts-from-tw-id.ts`

