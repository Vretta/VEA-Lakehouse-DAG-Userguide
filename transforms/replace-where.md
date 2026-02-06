# Replace Where Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/replace-where.ts`

## Overview

The `replace-where` transform replaces values in a target dataframe with values from a source dataframe based on matching keys.

## Function Signature

```typescript
export function replaceWhere(
  df_target: IRow[], 
  df_source: IRow[], 
  target_key: string[], 
  source_key: string[], 
  target_col: string, 
  source_col: string, 
  indicator_col?: string
): IRow[]
```

## Parameters

- `df_target`: Target dataframe to modify
- `df_source`: Source dataframe with replacement values
- `target_key`: Key columns in target dataframe
- `source_key`: Key columns in source dataframe
- `target_col`: Column to replace in target
- `source_col`: Column to use from source
- `indicator_col`: Optional column to mark replaced rows

## Behavior

Matches rows between target and source based on keys, then replaces target column values with source column values. Optionally marks replaced rows.

## Example

```typescript
{
  df_target: "students",
  df_source: "updated_scores",
  target_key: ["student_id"],
  source_key: ["student_id"],
  target_col: "score",
  source_col: "new_score",
  indicator_col: "was_updated"
}
```

## Related Transforms

- [Set Where](./set-where.md): Conditional value setting
- [Merge](./merge.md): Join dataframes

