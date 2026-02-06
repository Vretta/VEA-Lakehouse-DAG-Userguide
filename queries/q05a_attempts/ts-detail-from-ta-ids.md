# TS Detail from TA IDs

**Query Name:** `SQL_05A_TS_DETAIL_FROM_TA_ID`

## Description

Retrieves test session details from test attempt IDs, including teacher information.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `ts_id` | number | Test session ID |
| `ts_name` | string | Test session custom name |
| `ts_start` | timestamp | Test session start date/time |
| `num_students` | number | Total number of students |
| `num_students_started` | number | Number of students who started |
| `num_students_submitted` | number | Number of students who submitted |
| `ts_is_closed` | boolean | Whether the session is closed |
| `teacher_uid` | number | Teacher user ID |
| `teacher_email` | string | Teacher email |
| `teacher_name` | string | Teacher full name (first + last) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/ts-detail-from-ta-ids.ts`

