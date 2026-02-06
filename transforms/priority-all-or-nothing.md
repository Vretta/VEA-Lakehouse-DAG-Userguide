# Priority All Or Nothing Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/priority-all-or-nothing.ts`

## Overview

The `priority-all-or-nothing` transform determines an outcome based on priority order, returning the first matching priority found in a group.

## Function Signature

```typescript
export function priority_all_or_nothing(
  df_input: IRow[], 
  group_by: string[], 
  comparison_col: string, 
  priorities: string[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `group_by`: Columns to group by
- `comparison_col`: Column to check for priority values
- `priorities`: Array of priority values in order (highest to lowest)

## Behavior

For each group, iterates through priorities in order and returns the first matching value found. Also sets a `score` column from domain_label='time' if present.

## Usage

Used for assessment outcome determination where certain outcomes take priority over others.

## Related Transforms

- [Group By](./group-by.md): Similar grouping with different aggregation logic

