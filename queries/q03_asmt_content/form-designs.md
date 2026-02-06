# Form Designs

**Query Name:** `SQL_03_ASMT_CONTENT_FORM_DESIGNS`

## Description

Retrieves form design information for test window allocations, including form codes, component slugs, and course information.

## Required Inputs

- `twtar_ids` (number[]): Array of test window TD allocation rule IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `twtar_id` | number | Test window TD allocation rule ID |
| `test_window_id` | number | Test window ID |
| `test_term` | string | Test term (window code) |
| `test_design_id` | number | Test design ID |
| `type_slug` | string | Type slug |
| `course_name_full` | string | Full course name |
| `course_code` | string | Course code (foreign) |
| `lang` | string | Language code |
| `form_code` | string | Form code |
| `component_slug` | string | Component slug (foreign component code) |
| `is_secured` | boolean | Whether the assessment is secured |
| `is_questionnaire` | boolean | Whether this is a questionnaire |
| `tqr_ovrd_td_id` | number | TQR override test design ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/form-designs.ts`

