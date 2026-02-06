# Student Admin Meta Booklets

**Query Name:** `SQL_04B_REGISTRATIONS_STUDENT_ADMIN_META_BOOKLETS`

## Description

Retrieves student booklet assignments for test windows.

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
| `booklet_index` | string | Booklet index value |
| `booklet_pasi_course_code` | string | Booklet PASI course code (asmt_type_slug) |
| `n_intersect` | number | Number of intersections (count) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-admin-meta-booklets.ts`

