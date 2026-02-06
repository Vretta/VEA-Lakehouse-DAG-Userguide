# Cutoffs Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/cutoffs.ts`

## Overview

The `cutoffs` transform generates cutscore definitions based on score distributions and upper bounds.

## Function Signature

```typescript
export function cutoffs(
  df_input: IRow[], 
  group_by: string[], 
  col_target: string, 
  upper_bounds: number[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe with scores
- `group_by`: Columns to group by
- `col_target`: Score column
- `upper_bounds`: Array of upper bound values for cutoffs

## Output

Generates cutscore definitions that can be used with [Apply Cutscores](./apply-cutscores.md).

## Related Transforms

- [Apply Cutscores](./apply-cutscores.md): Uses output of this transform

