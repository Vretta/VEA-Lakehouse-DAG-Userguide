# Test Designs

**Query Name:** `SQL_03_ASMT_CONTENT_TEST_DESIGNS`

## Description

Retrieves basic test design information.

## Required Inputs

- `td_ids` (number[]): Array of test design IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Test design ID |
| `name` | string | Test design name |
| `created_on` | timestamp | Creation timestamp |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q03_asmt_content/test-designs.ts`

