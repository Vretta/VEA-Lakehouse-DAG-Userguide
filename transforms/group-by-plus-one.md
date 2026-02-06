# Group By Plus One Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/group-by-plus-one.ts`

## Overview

The `group-by-plus-one` transform performs grouping with an additional "overall" aggregation that includes all groups plus one overall group.

## Function Signature

```typescript
export function overall_plus_one(
  df_input: IRow[], 
  cols: string[], 
  agg: IRow[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `cols`: Columns to group by
- `agg`: Aggregation definitions

## Behavior

Similar to [Group By](./group-by.md) but adds an additional row with overall aggregations across all groups.

## Related Transforms

- [Group By](./group-by.md): Standard grouping without overall aggregation

