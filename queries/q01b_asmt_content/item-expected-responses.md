# Item Expected Responses

**Query Name:** `SQL_03_ASMT_CONTENT_ITEM_EXPECTED_RESPONSES`

## Description

Retrieves expected responses for items, including formatted responses, coded responses, and scoring information.

## Required Inputs

- `item_ids` (string[]): Array of item/question IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `tqer_id` | number | Test question expected response ID |
| `item_id` | string | Item/question ID |
| `lang` | string | Language code |
| `formatted_response` | string | Formatted response |
| `coded_response` | string | Coded response |
| `score` | number | Score value |
| `score_max` | number | Maximum score (weight) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/item-expected-responses.ts`

