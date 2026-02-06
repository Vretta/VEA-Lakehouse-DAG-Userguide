# Student Admin Meta

**Query Name:** `SQL_04B_REGISTRATIONS_STUDENT_ADMIN_META`

## Description

Retrieves student administrative metadata for test windows, including booklet assignments.

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
| `key_namespace` | string | Key namespace |
| `key` | string | Metadata key (e.g., 'BookletIndex') |
| `value` | string | Metadata value |
| `asmt_type_slug` | string | Assessment type slug |
| `meta` | json | Additional metadata |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-admin-meta.ts`

