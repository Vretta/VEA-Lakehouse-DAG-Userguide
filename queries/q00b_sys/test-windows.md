# Test Windows

**Query Name:** `SQL_01_SYS_TEST_WINDOWS`

## Description

Retrieves test window information including dates, codes, and configuration flags.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `test_window_id` | number | Test window ID |
| `tw_type_slug` | string | Test window type slug |
| `is_qa` | boolean | Whether this is a QA test window |
| `is_active` | boolean | Whether the test window is active |
| `is_bg` | boolean | Background flag |
| `is_for_pasi` | boolean | Whether this is for PASI sync |
| `date_start` | date | Test window start date |
| `date_end` | date | Test window end date |
| `window_code` | string | Test window code |
| `window_date_human` | string | Human-readable window date |
| `PASI_school_year` | string | PASI school year |
| `title` | string | Test window title |
| `next_tw_id` | number | Next test window ID |
| `is_duration_enforced` | boolean | Whether duration is enforced |
| `hardstop_offset_h` | number | Hard stop offset in hours |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q01_sys/test-windows.ts`

