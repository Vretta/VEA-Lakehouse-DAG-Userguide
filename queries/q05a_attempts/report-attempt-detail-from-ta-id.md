# Report Attempt Detail from TA ID

**Query Name:** `SQL_05A_REPORT_ATTEMPTS_DETAIL_FROM_TA_ID`

## Description

Retrieves report-ready attempt detail information including student demographics.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

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
| `form_code` | string | Form code |
| `student_uid` | number | Student user ID |
| `attempt_id` | number | Test attempt ID |
| `student_gov_id` | string | Student government ID |
| `gender` | string | Gender |
| `student_fname` | string | Student first name |
| `student_lname` | string | Student last name |
| `started_on_date` | string | Started on date (YYYY-MM-DD) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/report-attempt-detail-from-ta-id.ts`

