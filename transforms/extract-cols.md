# Extract Columns Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/extract-cols.ts`

## Overview

The `extract-cols` transform extracts nested properties from a column (typically JSON) into separate columns.

## Function Signature

```typescript
export function extractCols(
  df_input: IRow[], 
  source_prop: string, 
  new_cols?: {[key: string]: string}, 
  foreign_key_cols?: {[key: string]: string}
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `source_prop`: Column containing nested data (typically JSON)
- `new_cols`: Mapping of property paths to new column names
- `foreign_key_cols`: Mapping for foreign key relationships

## Usage

Extracts nested JSON properties or object properties into flat columns.

## Example

```typescript
{
  df_input: "students",
  source_prop: "metadata",
  new_cols: {
    "metadata.grade": "grade",
    "metadata.section": "section"
  }
}
```

## Related Transforms

- [Restrict Cols](./restrict-cols.md): Select specific columns

