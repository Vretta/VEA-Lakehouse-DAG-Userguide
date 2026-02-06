# Item Scale Register

**Query Name:** `SQL_03_ASMT_CONTENT_ITEM_SCALE_REGISTER`

## Description

Retrieves item scale register information for test window allocations.

## Required Inputs

- `twtar_ids` (number[]): Array of test window TD allocation rule IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `course_name_full` | string | Full course name |
| `test_term` | string | Test term (window code) |
| `item_id` | string | Item/question ID |
| `item_name` | string | Item/question label |
| `item_scale_nbr` | string | Item scale number (student question) |
| `report_label_short` | string | Short report label (concepts code) |
| `test_design_id` | number | Test design ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/item-scale-register.ts`

