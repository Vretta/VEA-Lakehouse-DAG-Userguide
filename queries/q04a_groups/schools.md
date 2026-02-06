# Schools

**Query Name:** `SQL_04A_GROUPS_SCHOOLS`

## Description

Retrieves school and school district information.

## Required Inputs

- `include_sample_schools` (boolean): Whether to include sample schools

## Optional Inputs

- `schl_dist_group_ids` (number[]): Filter by school district group IDs

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `s_id` | number | School ID |
| `s_gid` | number | School group ID |
| `s_code` | string | School code (foreign ID) |
| `s_name` | string | School name |
| `s_lang` | string | School language |
| `s_city` | string | School city |
| `s_country` | string | School country |
| `sd_id` | number | School district ID |
| `sd_gid` | number | School district group ID |
| `sd_code` | string | School district code (foreign ID) |
| `sd_name` | string | School district name |
| `sd_lang` | string | School district language (board language) |
| `is_sample` | boolean | Whether this is a sample school district |
| `is_sandbox` | boolean | Whether this is a sandbox school |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04a_groups/schools.ts`

