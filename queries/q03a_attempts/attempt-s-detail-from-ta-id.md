# Attempt S Detail from TA ID

**Query Name:** `SQL_05A_ATTEMPTS_S_DETAIL_FROM_TA_ID`

## Description

Retrieves simplified attempt detail information with section and question indices.

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
| `sc_name` | string | School class name |
| `sc_id` | number | School class ID |
| `assessment_code` | string | Assessment code (scts.slug) |
| `student_uid` | number | Student user ID |
| `attempt_id` | number | Test attempt ID |
| `question_index` | number | Question index |
| `section_index` | number | Section index |
| `form_code` | string | Form code |
| `sub_window_code` | string | Sub-window code |
| `is_started` | boolean | Whether the attempt has started |
| `started_on_date` | string | Started on date (YYYY-MM-DD) |
| `last_touch_on_date` | string | Last touch on date (YYYY-MM-DD) |
| `twtar_id` | number | Test window TD allocation rule ID |
| `test_window_id` | number | Test window ID |
| `tw_type_slug` | string | Test window type slug |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/attempt-s-detail-from-ta-id.ts`

