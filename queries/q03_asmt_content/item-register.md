# Item Register

**Query Name:** `SQL_03_ASMT_CONTENT_ITEM_REGISTER`

## Description

Retrieves item register information for test designs, including item IDs, labels, scoring information, and metadata.

## Required Inputs

- `td_ids` (number[]): Array of test design IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `tqr_id` | number | Test question register ID |
| `item_id` | string | Item/question ID |
| `item_name` | string | Item/question label |
| `lang` | string | Language code |
| `test_design_id` | number | Test design ID |
| `is_questionnaire` | boolean | Whether this is a questionnaire item |
| `is_reading_passage` | boolean | Whether this is a reading passage |
| `score_profile_id` | number | Score profile ID |
| `score_points` | number | Score points |
| `is_human_scored` | boolean | Whether this item requires human scoring |
| `expected_answer` | string | Expected answer |
| `item_nbr` | string | Student question number |
| `report_label_short` | string | Short report label (concepts code) |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/item-register.ts`

