# TQER Item Exceptions

**Query Name:** `SQL_05B_TQER_ITEM_EXCEPTIONS`

## Description

Retrieves expected response item exceptions from test question expected responses, including score overrides.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs
- `item_ids` (string[]): Array of item/question IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `item_id` | string | Item/question ID |
| `lang` | string | Language code |
| `match_response_value` | string | Match response value (formatted_response) |
| `score_override` | number | Score override value |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05b_responses/tqer-item-exceptions.ts`

