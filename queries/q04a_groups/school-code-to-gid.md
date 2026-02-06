# School Code to GID

**Query Name:** `SQL_SCHL_CODE_TO_GID`

## Description

Maps school codes to school and school district group IDs.

## Required Inputs

- `s_codes` (string[]): Array of school codes (foreign IDs)

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `s_id` | number | School ID |
| `s_gid` | number | School group ID |
| `s_code` | string | School code (foreign ID) |
| `sd_code` | string | School district code (foreign ID) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04a_groups/school-code-to-gid.ts`

