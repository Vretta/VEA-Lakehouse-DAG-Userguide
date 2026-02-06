# Student Item Exceptions

**Query Name:** `SQL_05B_STUDENT_ITEM_EXCEPTIONS`

## Description

Retrieves student-specific item exceptions, including response value and score overrides.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `item_id` | string | Item/question ID |
| `uid` | number | User ID |
| `match_response_value` | string | Match response value |
| `new_response_value` | string | New response value override |
| `new_score` | number | New score override |
| `is_score_override` | boolean | Whether this is a score override |
| `is_response_value_override` | boolean | Whether this is a response value override |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05b_responses/student-item-exceptions.ts`

