# Fill NA Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/fill-na.ts`

## Overview

The `fill-na` transform fills null/undefined values in specified columns with default values.

## Function Signatures

```typescript
export function fillNaMulti(
  df_input: IRow[], 
  values: {[key: string]: Value}
): IRow[]

export function fillNaMultiInplace(
  df_input: IRow[], 
  values: {[key: string]: Value}
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `values`: Object mapping column names to fill values

## Example

```typescript
{
  df_input: "students",
  values: {
    "score": 0,
    "status": "unknown"
  },
  inplace: false
}
```

## Notes

- Can fill multiple columns in a single operation
- `inplace` option modifies original rows vs creating new ones

## Related Transforms

- [Set Where](./set-where.md): Conditional value setting

