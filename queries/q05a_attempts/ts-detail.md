# TS Detail

**Query Name:** `SQL_05A_ATTEMPTS_TS_DETAIL`

## Description

Retrieves test session details including student counts and teacher information.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

- `include_sample_assessments` (boolean): Include sample assessments (default: false)

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `ts_id` | number | Test session ID |
| `s_name` | string | School name |
| `s_code` | string | School code (foreign ID) |
| `sc_name` | string | School class name |
| `assessment_code` | string | Assessment code (scts.slug) |
| `date_time_start` | timestamp | Session start date/time |
| `is_closed` | boolean | Whether the session is closed |
| `num_students` | number | Total number of students |
| `num_students_started` | number | Number of students who started |
| `num_students_submitted` | number | Number of students who submitted |
| `teacher_uid` | number | Teacher user ID |
| `teacher_email` | string | Teacher email |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/ts-detail.ts`

