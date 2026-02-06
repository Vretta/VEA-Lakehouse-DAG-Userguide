# Scan Info from TA ID

**Query Name:** `SQL_07_SCAN_INFO_FROM_TA_ID`

## Description

Retrieves scan information for test attempts, including scan counts, upload dates, and missing scan information.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `attempt_id` | number | Test attempt ID |
| `num_scans_required` | number | Number of scans required (based on submitted status and paper response items) |
| `num_scans_uploaded` | number | Number of scans uploaded |
| `num_scans_missing` | number | Number of scans missing (calculated) |
| `is_any_uploaded` | boolean | Whether any scans have been uploaded |
| `first_scan_uploaded_on` | timestamp | First scan upload timestamp |
| `last_scan_uploaded_on` | timestamp | Last scan upload timestamp |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q07_scans/scan-info-from-ta-id.ts`

