# School Groupings

**Query Name:** `SQL_04B_REGISTRATIONS_SCHOOL_GROUPINGS`

## Description

Retrieves school class groupings (school classes) for test windows and schools.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs
- `s_ids` (number[]): Array of school IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `sc_id` | number | School class ID |
| `sc_gid` | number | School class group ID |
| `s_gid` | number | School group ID |
| `sc_name` | string | School class name |
| `access_code` | string | Access code |
| `is_active` | boolean | Whether the class is active |
| `is_placeholder` | boolean | Whether this is a placeholder class |
| `group_type` | string | Group type |
| `semester_id` | number | Semester ID |
| `created_on` | timestamp | Creation timestamp |
| `deactivated_on` | timestamp | Deactivation timestamp |
| `s_id` | number | School ID |
| `sc_ss_tw_id` | number | School semester test window ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/school-groupings.ts`

