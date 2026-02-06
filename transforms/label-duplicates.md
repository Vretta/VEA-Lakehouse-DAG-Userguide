# Label Duplicates Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/label-duplicates.ts`

## Overview

The `label-duplicates` transform identifies and labels duplicate rows instead of removing them.

## Function Signature

```typescript
export function label_duplicates(
  df_input: IRow[], 
  subset: string[], 
  col: string
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `subset`: Columns to consider for duplicate detection
- `col`: Column name to add duplicate label to

## Behavior

Adds a label column indicating if a row is a duplicate based on the subset columns.

## Related Transforms

- [Drop Duplicates](./drop-duplicates.md): Removes duplicates instead of labeling

