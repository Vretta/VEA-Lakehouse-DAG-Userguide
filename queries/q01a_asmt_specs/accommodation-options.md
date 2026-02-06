# Accommodation Options

**Query Name:** `SQL_02_ASMT_SPECS_ACCOMMODATION_OPTIONS`

## Description

Retrieves available accommodation options for assessment types.

## Required Inputs

- `tw_type_slugs` (string[]): Array of test window type slugs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `acc_id` | number | Accommodation ID |
| `accommodation_number` | string | Accommodation number (foreign ID) |
| `accommodation_name` | string | Accommodation name |
| `value_data_type` | string | Accommodation value data type |
| `acc_type_slug` | string | Accommodation type slug |
| `sort_order` | number | Sort order |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q02_asmt_specs/accommodation-options.ts`

