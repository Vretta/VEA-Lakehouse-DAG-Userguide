# Drop Duplicates Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/drop-duplicates.ts`

## Overview

The `drop-duplicates` transform removes duplicate rows from a dataframe based on specified columns.

## Function Signature

```typescript
export function drop_duplicates(
  df_input: IRow[], 
  subset?: string[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `subset`: Optional array of column names to consider for duplicates. If not provided, all columns are considered.

## Behavior

- If `subset` is provided, only those columns are considered for duplicate detection
- If `subset` is not provided, all columns must match for a row to be considered duplicate
- First occurrence is kept, subsequent duplicates are removed

## Example

```typescript
{
  df_input: "students",
  subset: ["student_id", "attempt_id"]
}
```

## Related Transforms

- [Label Duplicates](./label-duplicates.md): Marks duplicates instead of removing

