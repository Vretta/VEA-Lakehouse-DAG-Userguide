# School Student Enrolments

**Query Name:** `SQL_SCHOOL_STUDENT_ENROLMENTS`

## Description

Retrieves school student enrolment mappings for test windows.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs
- `s_gids` (number[]): Array of school group IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `s_gid` | number | School group ID |
| `uid` | number | User ID |
| `type_slug` | string | Type slug |
| `sc_gid` | number | School class group ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/school-student-enrolments.ts`

