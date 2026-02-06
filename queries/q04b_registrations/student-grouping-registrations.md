# Student Grouping Registrations

**Query Name:** `SQL_04B_REGISTRATIONS_STU_G_REGSTRATIONS`

## Description

Retrieves student registrations to school classes (groupings).

## Required Inputs

- `sc_gids` (number[]): Array of school class group IDs

## Optional Inputs

- `uids` (number[]): Filter by user IDs
- `limit` (number): Maximum number of records to return

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `ur_id` | number | User role ID |
| `uid` | number | User ID |
| `sc_gid` | number | School class group ID |
| `created_on` | timestamp | Creation timestamp |
| `is_walkin` | boolean | Whether this is a walk-in student (abed_nperson_student) |
| `is_revoked` | boolean | Whether the registration is revoked |
| `revoked_on` | timestamp | Revocation timestamp |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-grouping-registrations.ts`

