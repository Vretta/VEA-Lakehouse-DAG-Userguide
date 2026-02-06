# Codebook Table Fields

**Query Name:** `SQL_00_META_CODEBOOK_TABLE_FIELDS`

## Description

Retrieves field definitions for codebook tables, including data types, nullability, primary/foreign key information, and relationships.

## Required Inputs

- `data_def_table_ids` (number[]): Array of data definition table IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Field ID |
| `table_slug` | string | Table slug this field belongs to |
| `slug` | string | Field slug identifier |
| `caption` | string | Human-readable field name |
| `type` | string | Field data type |
| `description` | string | Field description |
| `is_nullable` | boolean | Whether the field allows null values |
| `is_primary_key` | boolean | Whether this field is a primary key |
| `is_foreign_key` | boolean | Whether this field is a foreign key |
| `is_foreign_derived` | boolean | Whether this field is foreign-derived |
| `fk_table_type` | string | Foreign key table type |
| `fk_table_slug` | string | Foreign key table slug |
| `fk_field_slug` | string | Foreign key field slug |
| `fd_table_type` | string | Foreign-derived table type |
| `fd_field_slug` | string | Foreign-derived field slug |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q00_meta/codebook-table-fields.ts`

