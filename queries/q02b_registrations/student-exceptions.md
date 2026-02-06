# Student Exceptions

**Query Name:** `SQL_04B_STUDENT_EXCEPTIONS`

## Description

Retrieves student exceptions for test windows.

## Required Inputs

- `uids` (number[]): Array of user IDs
- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Exception ID |
| `test_window_id` | number | Test window ID |
| `uid` | number | User ID |
| `school_group_id` | number | School group ID |
| `twtdar_id` | number | Test window TD allocation rule ID |
| `school_class_id` | number | School class ID |
| `category` | string | Exception category |
| `is_pended` | boolean | Whether the exception is pended |
| `attempt_id` | number | Test attempt ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-exceptions.ts`

