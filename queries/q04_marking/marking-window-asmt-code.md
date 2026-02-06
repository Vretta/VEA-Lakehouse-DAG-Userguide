# Marking Window ASMT Code

**Query Name:** `SQL_06_MARKING_WINDOW_ASMT_CODE`

## Description

Retrieves assessment codes (type slugs) for marking windows with item and scale counts.

## Required Inputs

- `mw_ids` (number[]): Array of marking window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `marking_window_id` | number | Marking window ID |
| `twtar_type_slug` | string | Test window TD allocation rule type slug |
| `twtar_n_marked_items` | number | Number of marked items |
| `twtar_n_marked_scales` | number | Number of marked scales |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q06_marking/marking-window-asmt-code.ts`

