# Test Window Allocation Rules

**Query Name:** `SQL_02_ASMT_SPECS_TEST_WINDOW_ALLOC_RULES`

## Description

Retrieves test window to test design allocation rules, including form codes, component slugs, security settings, and course information.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

- `twtar_type_slugs` (string[]): Filter by type slugs
- `sub_window_codes` (string[]): Filter by sub-window codes
- `include_sample_assessments` (boolean): Include sample assessments (default: false)
- `include_questionnaire` (boolean): Include questionnaires (default: false)
- `only_secured_twtdar` (boolean): Only return secured allocations (default: false)
- `only_human_marked_twtdar` (boolean): Only return human-marked allocations (default: false)
- `restrict_twtar_ids` (number[]): Restrict to specific allocation rule IDs
- `restrict_twtar_form_codes` (string[]): Restrict to specific form codes

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `twtar_id` | number | Test window TD allocation rule ID |
| `is_active` | boolean | Whether the allocation is active |
| `test_window_id` | number | Test window ID |
| `type_slug` | string | Type slug |
| `type_slug_equiv` | string | Equivalent type slug (falls back to type_slug) |
| `form_code` | string | Form code |
| `component_slug` | string | Component slug |
| `is_secured` | boolean | Whether the assessment is secured |
| `is_questionnaire` | boolean | Whether this is a questionnaire |
| `is_sample` | boolean | Whether this is a sample assessment |
| `is_field_test` | boolean | Whether this is a field test |
| `is_active_for_qa` | boolean | Whether active for QA |
| `is_swap_risk` | boolean | Swap risk flag |
| `lang` | string | Language code |
| `td_id` | number | Test design ID (uses override if available) |
| `window_code` | string | Window code (uses sub_window_code if available) |
| `alloc_td_id` | number | Allocated test design ID |
| `override_td_id` | number | Override test design ID |
| `long_name` | string | Long name |
| `test_duration` | number | Test duration |
| `selection_order` | number | Selection order |
| `is_school_allowed_strict` | boolean | Strict school allowance flag |
| `is_classroom_common_form` | boolean | Classroom common form flag |
| `caption_short` | string | Short caption |
| `course_code` | string | Course code |
| `cover_security_msg` | string | Cover security message |
| `is_simple_cover` | boolean | Simple cover flag |
| `foreign_component_code` | string | Foreign component code |
| `is_perusal_allow` | boolean | Perusal allowed flag |
| `is_download_results` | boolean | Download results allowed |
| `is_local_score` | boolean | Local score flag |
| `resource_td_id` | number | Resource test design ID |
| `resource_caption` | string | Resource caption |
| `course_code_foreign` | string | Foreign course code |
| `course_name_short` | string | Short course name |
| `course_name_full` | string | Full course name |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q02_asmt_specs/test-window-alloc-rules.ts`

