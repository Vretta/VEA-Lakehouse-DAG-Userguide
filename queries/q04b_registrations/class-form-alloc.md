# Class Form Allocation

**Query Name:** `SQL_04B_REGISTRATIONS_CLASS_FORM_ALLOC`

## Description

Retrieves class form allocation information, showing which forms are allocated to classes and usage statistics.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | School class common form ID |
| `type_slug` | string | Type slug |
| `is_active` | boolean | Whether the allocation is active |
| `school_class_id` | number | School class ID |
| `test_form_id` | number | Test form ID |
| `n_students` | number | Number of students using this form |
| `max_ta_started_on` | timestamp | Maximum test attempt start time |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/class-form-alloc.ts`

