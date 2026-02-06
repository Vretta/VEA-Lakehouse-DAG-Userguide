# Form Module Items

**Query Name:** `SQL_03_ASMT_CONTENT_FORM_MODULE_ITEMS`

## Description

Retrieves form module item mappings, showing how items are organized within test forms and modules.

## Required Inputs

- `twtar_ids` (number[]): Array of test window TD allocation rule IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_design_id` | number | Test design ID |
| `test_panel_id` | number | Test panel ID |
| `question_id` | string | Question/item ID |
| `module_id` | number | Module ID |
| `section_num` | number | Section number |
| `lang` | string | Language code |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/form-module-items.ts`

