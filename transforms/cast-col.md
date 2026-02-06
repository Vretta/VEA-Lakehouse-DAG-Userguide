# Cast Column Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/cast-col.ts`

## Overview

The `cast-col` transform converts a column to a different data type.

## Function Signature

```typescript
export function cast_col(
  df_input: IRow[], 
  col_target: string, 
  target_type: string
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `col_target`: Column to cast
- `target_type`: Target data type

## Supported Types

- `number`, `integer`, `float`
- `string`
- `boolean`
- `date`, `datetime`

## Example

```typescript
{
  df_input: "students",
  col_target: "score",
  target_type: "string"
}
```

## Related Transforms

- [Map Col](./map-col.md): More complex type conversions with operations

