# TAQR from TA ID

**Query Name:** `SQL_05B_TAQR_FROM_TA_ID`

## Description

Retrieves test attempt question responses (TAQR) for specified test attempts.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `taqr_id` | number | Test attempt question response ID |
| `ta_id` | number | Test attempt ID |
| `item_id` | string | Item/question ID |
| `score` | number | Score value |
| `weight` | number | Weight value |
| `is_nr` | boolean | Whether this is a non-response |
| `is_invalid` | boolean | Whether the response is invalid |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05b_responses/taqr-from-ta-id.ts`

