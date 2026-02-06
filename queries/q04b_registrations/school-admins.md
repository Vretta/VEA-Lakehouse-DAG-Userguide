# School Admins

**Query Name:** `SQL_04B_REGISTRATIONS_SCHOOL_ADMINS`

## Description

Retrieves school administrator information.

## Required Inputs

- `s_gids` (number[]): Array of school group IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `s_name` | string | School name |
| `s_code` | string | School code (foreign ID) |
| `first_name` | string | Admin first name |
| `last_name` | string | Admin last name |
| `contact_email` | string | Contact email (excludes @vretta.com emails) |
| `is_claimed` | boolean | Whether the account is claimed |
| `created_on` | timestamp | Creation timestamp |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/school-admins.ts`

