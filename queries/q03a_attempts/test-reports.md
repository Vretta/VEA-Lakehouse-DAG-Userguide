# Test Reports

**Query Name:** `SQL_05A_ATTEMPTS_TEST_REPORTS`

## Description

Retrieves test report information for test attempts.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `uid` | number | User ID |
| `test_attempt_id` | number | Test attempt ID |
| `is_successful` | boolean | Whether the test was successful |
| `is_no_show` | boolean | Whether this was a no-show |
| `institution_name` | string | Institution name |
| `num_questions_answered` | number | Number of questions answered |
| `num_questions_total` | number | Total number of questions |
| `created_on` | timestamp | Creation timestamp |
| `test_session_date_time_start` | timestamp | Test session start date/time |
| `is_invalid` | boolean | Whether the report is invalid |
| `slug` | string | Report slug |
| `result_code` | string | Result code |
| `results_released_on` | timestamp | Results released timestamp |
| `raw_score` | number | Raw score |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/test-reports.ts`

