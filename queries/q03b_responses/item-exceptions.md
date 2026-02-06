# Item Exceptions

**Query Name:** `SQL_05B_ITEM_EXCEPTIONS`

## Description

Retrieves item-level exceptions for test windows, including score overrides and response value matches.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `is_score_override` | boolean | Whether this is a score override |
| `item_id` | string | Item/question ID |
| `item_label` | string | Item label |
| `lang` | string | Language code |
| `match_response_value` | string | Match response value |
| `score_override` | number | Score override value |
| `test_window_id` | number | Test window ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05b_responses/item-exceptions.ts`

