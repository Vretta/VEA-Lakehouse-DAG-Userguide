# Round Number Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/round-number.ts`

## Overview

The `round-number` transform rounds numeric values in specified columns to a given precision.

## Function Signatures

```typescript
export function roundNumber(
  df_input: IRow[], 
  columns: string[], 
  precision: number
): IRow[]

export function roundNumberString(
  df_input: IRow[], 
  columns: string[], 
  precision: number
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `columns`: Array of column names to round
- `precision`: Number of decimal places
- `as_string`: Whether to return as string (roundNumberString)

## Example

```typescript
{
  df_input: "scores",
  columns: ["score", "percentage"],
  precision: 2,
  as_string: false
}
```

## Notes

- Can round multiple columns in a single operation
- Precision can be sourced from another dataframe column
- `as_string` option returns rounded values as strings

## Related Transforms

- [Cast Col](./cast-col.md): Type conversion
- [Map Col](./map-col.md): More complex numeric operations

