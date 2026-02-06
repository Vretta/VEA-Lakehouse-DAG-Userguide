# Marker User Roles

**Query Name:** `SQL_04A_GROUPS_MARKER_USER_ROLES`

## Description

Retrieves user roles for marking windows.

## Required Inputs

- `marking_window_id` (number): Marking window ID

## Optional Inputs

None

## Output Schema

| Field | Type | Description |
|-------|------|-------------|
| `uid` | number | User ID |
| `role_type` | string | Role type |

## Source

`api--multiplex/src/services/public/data-exporter/data-export-queries/queries/q04a_groups/marker-user-roles.ts`

