# Item Register Consolidated

**Query Name:** `SQL_03_ASMT_CONTENT_ITEM_REGISTER_CONSOLIDATED`

## Description

Retrieves consolidated item register information with test window context, including form codes, component slugs, and course information.

## Required Inputs

- `twtar_ids` (number[]): Array of test window TD allocation rule IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `twtar_id` | number | Test window TD allocation rule ID |
| `test_term` | string | Test term (window code) |
| `type_slug` | string | Type slug |
| `course_name_full` | string | Full course name |
| `course_code` | string | Course code |
| `test_lang` | string | Test language |
| `form_code` | string | Form code |
| `component_slug` | string | Component slug (foreign component code) |
| `item_nbr` | string | Item number (student question) |
| `item_id` | string | Item/question ID |
| `response_lang` | string | Response language |
| `report_label_short` | string | Short report label (concepts code) |
| `item_name` | string | Item/question label |
| `joined_with_item_nbr` | string | Joined with item number (entry order) |
| `item_domain` | string | Item domain (temporary) |
| `is_reading_passage` | boolean | Whether this is a reading passage |
| `is_questionnaire` | boolean | Whether this is a questionnaire item |
| `text_rep` | string | Text representation (content_008, temporary) |
| `subject_code` | string | Subject code (content_001, temporary) |
| `subject_caption` | string | Subject caption (content_015, temporary) |
| `is_practice_test` | boolean | Practice test flag (temporary) |
| `test_design_id` | number | Test design ID |
| `score_points` | number | Score points |
| `is_human_scored` | boolean | Whether this item requires human scoring |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/item-register-consolidated.ts`

