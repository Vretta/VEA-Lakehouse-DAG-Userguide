# Sort By Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/sort-by.ts`

## Overview

The `sort-by` transform sorts rows in a dataframe by specified columns.

## Function Signature

```typescript
export function sort_by(
  df_input: IRow[], 
  cols: string[], 
  ascending?: boolean
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `cols`: Array of column names to sort by
- `ascending`: Sort order (true for ascending, false for descending, default: true)

## Example

```typescript
{
  df_input: "students",
  cols: ["school_id", "score"],
  ascending: false
}
```

## Notes

- Multiple columns can be specified for multi-level sorting
- Sort is stable (preserves order of equal elements)

## Related Transforms

- [Group By](./group-by.md): Often used before sorting

