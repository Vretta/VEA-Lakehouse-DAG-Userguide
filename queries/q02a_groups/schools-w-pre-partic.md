# Schools with Pre-Participation

**Query Name:** `SQL_04A_GROUPS_SCHOOLS_W_PRE_PARTIC`

## Description

Retrieves schools with pre-participation data, showing which assessment codes are allowed for each school in test windows.

## Required Inputs

- `s_ids` (number[]): Array of school IDs
- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_window_id` | number | Test window ID |
| `school_id` | number | School ID |
| `is_all_type_slugs` | boolean | Whether all type slugs are allowed |
| `asmt_codes` | string | Comma-separated list of assessment codes (type slugs) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04a_groups/schools-w-pre-partic.ts`

