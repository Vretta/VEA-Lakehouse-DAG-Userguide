# Students

**Query Name:** `SQL_04B_REGISTRATIONS_STUDENTS`

## Description

Retrieves student information including names, government IDs, date of birth, and gender.

## Required Inputs

- `uids` (number[]): Array of user IDs

## Optional Inputs

- `limit` (number): Maximum number of records to return

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `uid` | number | User ID |
| `stu_gov_id` | string | Student government ID (StudentIdentificationNumber) |
| `first_name` | string | Student first name |
| `last_name` | string | Student last name |
| `stu_dob` | string | Student date of birth |
| `gender` | string | Gender |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04b_registrations/students.ts`

