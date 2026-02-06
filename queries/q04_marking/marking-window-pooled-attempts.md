# Marking Window Pooled Attempts

**Query Name:** `SQL_06_MARKING_WINDOW_POOLED_ATTEMPTS`

## Description

Retrieves pooled attempts in marking windows, showing which attempts have been pooled for marking.

## Required Inputs

- `mw_ids` (number[]): Array of marking window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `marking_window_id` | number | Marking window ID |
| `ta_id` | number | Test attempt ID |
| `n_items` | number | Number of items |
| `n_scales` | number | Number of scales |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q06_marking/marking-window-pooled-attempts.ts`

