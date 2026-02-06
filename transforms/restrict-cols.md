# Restrict Columns Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/restrict-cols.ts`

## Overview

The `restrict-cols` transform selects only specified columns from a dataframe.

## Function Signature

```typescript
export function restrict_cols(
  df_input: IRow[], 
  cols: string[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `cols`: Array of column names to keep

## Example

```typescript
{
  df_input: "students",
  cols: ["student_id", "name", "score"]
}
```

## Notes

- Only specified columns are included in output
- Order of columns in output matches order in `cols` array
- Missing columns result in undefined values

## Related Transforms

- [Extract Cols](./extract-cols.md): Extract nested columns
- [Rename Cols](./rename-cols.md): Rename columns

