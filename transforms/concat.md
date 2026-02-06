# Concat Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/concat.ts`

## Overview

The `concat` transform concatenates multiple dataframes vertically (row-wise), combining them into a single dataframe.

## Function Signature

```typescript
export function concatDataFrames(dfs: IRow[][]): IRow[]
```

## Parameters

- `dfs`: Array of dataframes to concatenate

## Usage

Combines multiple dataframes with the same schema into one.

## Example

```typescript
{
  df_inputs: ["df1", "df2", "df3"]
}
```

## Notes

- Dataframes should have compatible schemas
- All columns from all dataframes are included
- Missing columns in some dataframes result in undefined values

## Related Transforms

- [Merge](./merge.md): Join dataframes horizontally

