# Rename Columns Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/rename-cols.ts`

## Overview

The `rename-cols` transform renames columns in a dataframe.

## Function Signatures

```typescript
export function renameCols(
  df_input: IRow[], 
  new_names: {[key: string]: string}
): IRow[]

export function renameColsInplace(
  df_input: IRow[], 
  new_names: {[key: string]: string}
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `new_names`: Object mapping old column names to new column names
- `inplace`: Whether to modify in place or create new rows

## Example

```typescript
{
  df_input: "students",
  new_names: {
    "student_id": "id",
    "student_name": "name"
  },
  inplace: false
}
```

## Related Transforms

- [Restrict Cols](./restrict-cols.md): Select columns
- [Extract Cols](./extract-cols.md): Extract nested columns

