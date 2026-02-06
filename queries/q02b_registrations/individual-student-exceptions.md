# Individual Student Exceptions

**Query Name:** `SQL_04B_INDIVIDUAL_STUDENT_EXCEPTIONS`

## Description

Retrieves individual student exceptions with action configuration details.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_window_id` | number | Test window ID |
| `twtar_id` | number | Test window TD allocation rule ID |
| `uid` | number | User ID |
| `test_attempt_id` | number | Test attempt ID |
| `category` | string | Exception category (always "INDIVIDUAL") |
| `pct_score` | number | Percentage score override (from action_config) |
| `outcome` | string | Outcome (from action_config) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/individual-student-exceptions.ts`

