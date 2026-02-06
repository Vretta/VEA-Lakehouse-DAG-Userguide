# Student Admin Meta Wide

**Query Name:** `SQL_04B_REGISTRATIONS_STUDENT_ADMIN_META_WIDE`

## Description

Retrieves student administrative metadata in a wide format, aggregating grade and homeroom information.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs
- `uids` (number[]): Array of user IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_window_id` | number | Test window ID |
| `uid` | number | User ID |
| `student_grade` | string | Student grade (comma-separated if multiple) |
| `class_homeroom` | string | Class homeroom (comma-separated if multiple) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-admin-meta-wide.ts`

