# Attempts from TW ID Testcentre

**Query Name:** `SQL_05A_ATTEMPTS_FROM_TW_ID_TESTCENTRE`

## Description

Retrieves test centre attempt information for certification bodies and institutions, filtered by test window IDs. Similar to attempts-testcenters but takes test window IDs as input.

## Required Inputs

- `tw_ids` (number[]): Array of test window IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `admin_window_id` | number | Admin window ID (test window ID) |
| `attempt_id` | number | Test attempt ID |
| `admin_window_name` | string | Admin window name (JSON extracted) |
| `TestingJID` | string | Testing jurisdiction ID (certification body foreign ID) |
| `jurisdiction_id` | number | Jurisdiction ID (certification body ID) |
| `jurisdiction_name` | string | Jurisdiction name (JSON extracted) |
| `test_center_id` | number | Test center ID (institution ID) |
| `test_center_name` | string | Test center name |
| `test_session_id` | number | Test session ID |
| `CAECUID` | number | CAEC user ID (test attempt UID) |
| `first_name` | string | User first name |
| `twtar_id` | number | Test window TD allocation rule ID |
| `lang` | string | Language code |
| `asmt_code` | string | Assessment code (type slug) |
| `form_code` | string | Form code |
| `is_print` | boolean | Print flag |
| `is_attempt_invalidated` | boolean | Whether the attempt is invalidated |
| `is_session_cancelled` | boolean | Whether the session is cancelled |
| `is_session_closed` | boolean | Whether the session is closed |
| `is_started` | timestamp/boolean | Start timestamp or boolean |
| `started_on` | timestamp | Start timestamp |
| `results_computed_on` | timestamp | Results computed timestamp |
| `results_released_on` | timestamp | Results released timestamp |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q05a_attempts/attempt-detail-from-ta-id.ts`

