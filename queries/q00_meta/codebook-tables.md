# Codebook Tables

**Query Name:** `SQL_00_META_CODEBOOK_TABLES`

## Description

Retrieves metadata about codebook tables (data definition tables) in the system. These tables define the structure and schema of data assets.

## Required Inputs

None

## Optional Inputs

- `asset_slugs` (string[]): Filter by specific asset slugs

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Table ID |
| `slug` | string | Table slug identifier |
| `caption` | string | Human-readable table name |
| `life_cycle_group` | string | Life cycle group classification |
| `description` | string | Table description |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q00_meta/codebook-tables.ts`

