# Student Gov ID to UID

**Query Name:** `SQL_STUDENT_GOV_ID_TO_UID`

## Description

Maps student government IDs to user IDs.

## Required Inputs

- `stu_gov_meta_key` (string): User meta key for government ID (typically 'StudentIdentificationNumber')
- `stu_gov_ids` (string[]): Array of student government IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `uid` | number | User ID |
| `stu_gov_id` | string | Student government ID |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-gov-id-to-uid.ts`

