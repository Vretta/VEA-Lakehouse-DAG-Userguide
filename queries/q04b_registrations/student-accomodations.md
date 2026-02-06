# Student Accommodations

**Query Name:** `SQL_04B_REGISTRATIONS_STUDENT_ACCOMMODATIONS`

## Description

Retrieves student accommodation assignments.

## Required Inputs

- `uids` (number[]): Array of user IDs
- `acc_ids` (number[]): Array of accommodation IDs

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `ua_id` | number | User accommodation ID |
| `uid` | number | User ID |
| `acc_id` | number | Accommodation ID |
| `accommodation_value` | string | Accommodation value |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/student-accomodations.ts`

