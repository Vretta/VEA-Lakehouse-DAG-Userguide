# Marking Windows

**Query Name:** `SQL_06_MARKING_WINDOWS`

## Description

Retrieves marking window information for test windows, including dates, status flags, and configuration.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_window_id` | number | Test window ID |
| `marking_window_id` | number | Marking window ID |
| `mwtw_created_on` | timestamp | Marking window test window creation timestamp |
| `mwtw_is_material` | boolean | Whether this is material |
| `mw_name` | string | Marking window name |
| `mw_start_on` | timestamp | Marking window start date |
| `mw_end_on` | timestamp | Marking window end date |
| `mw_is_scoring_disabled` | boolean | Whether scoring is disabled |
| `mw_is_active` | boolean | Whether the marking window is active |
| `mw_is_archived` | boolean | Whether the marking window is archived |
| `mw_is_hidden_for_scorers` | boolean | Whether hidden for scorers |
| `mw_is_rescore` | boolean | Whether this is a rescore |
| `is_scan_reassign_allowed` | boolean | Whether scan reassignment is allowed |
| `is_scorer_annotation_allowed` | boolean | Whether scorer annotation is allowed |
| `mw_is_test_centre` | boolean | Whether this is a test centre marking window |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q06_marking/marking-windows.ts`

