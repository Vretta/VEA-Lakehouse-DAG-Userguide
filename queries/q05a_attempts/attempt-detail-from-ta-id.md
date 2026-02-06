# Attempt Detail from TA ID

**Query Name:** `SQL_05A_ATTEMPTS_DETAIL_FROM_TA_ID`

## Description

Retrieves detailed attempt information including student information, scores, and timing data.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

- `uids` (number[]): Filter by user IDs

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `s_name` | string | School name |
| `s_code` | string | School code (foreign ID) |
| `sd_code` | string | School district code (foreign ID) |
| `sd_name` | string | School district name |
| `sd_is_sample` | boolean | Whether this is a sample school district |
| `sc_name` | string | School class name |
| `sc_id` | number | School class ID |
| `assessment_code` | string | Assessment code (scts.slug) |
| `student_uid` | number | Student user ID |
| `attempt_id` | number | Test attempt ID |
| `form_code` | string | Form code |
| `sub_window_code` | string | Sub-window code |
| `student_gov_id` | string | Student government ID |
| `student_fname` | string | Student first name |
| `student_lname` | string | Student last name |
| `time_spent_min` | number | Time spent in minutes |
| `num_screens_accessed` | number | Number of screens accessed |
| `machine_score` | number | Machine score (rounded to 2 decimals) |
| `machine_weight` | number | Machine weight (total) |
| `last_touch_on` | timestamp | Last touch timestamp |
| `started_on_date` | string | Started on date (YYYY-MM-DD) |
| `closed_on_date` | string | Closed on date (YYYY-MM-DD) |
| `ta_is_submitted` | boolean | Whether the attempt is submitted |
| `twtar_id` | number | Test window TD allocation rule ID |
| `test_window_id` | number | Test window ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/attempt-detail-from-ta-id.ts`

