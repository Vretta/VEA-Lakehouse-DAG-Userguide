# Scan Count by Date from TA IDs

**Query Name:** `SQL_07_SCAN_COUNT_BY_DATE_FROM_TA_ID`

## Description

Retrieves scan counts grouped by upload date for test attempts.

## Required Inputs

- `ta_ids` (number[]): Array of test attempt IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `upload_date` | string | Upload date (YYYY-MM-DD) |
| `n_scans` | number | Number of scans uploaded on this date |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q07_scans/scan-count-by-date-from-ta-ids.ts`

