# Pivot Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/pivot.ts`

## Overview

The `pivot` transform reshapes data from long to wide format, creating columns from values in a specified column.

## Function Signature

```typescript
export function pivot(
  data: IRow[], 
  rowHeader: string[], 
  colHeader: string, 
  colValue: string, 
  aggregator: AggFunc, 
  colPrefix?: string, 
  defaultValue?: Value
): IRow[]
```

## Parameters

- `data`: Input dataframe
- `rowHeader`: Columns to use as row identifiers
- `colHeader`: Column whose values become new column names
- `colValue`: Column whose values populate the pivoted cells
- `aggregator`: Aggregation function (first, last, max, min, mean, count)
- `colPrefix`: Optional prefix for new column names
- `defaultValue`: Default value for missing combinations

## Example

### Input

```json
[
  {"student_id": 1, "domain": "Reading", "score": 85},
  {"student_id": 1, "domain": "Writing", "score": 90},
  {"student_id": 2, "domain": "Reading", "score": 75}
]
```

### Configuration

```typescript
{
  df_input: "scores",
  group_by: ["student_id"],
  index: "domain",
  value: "score",
  agg: "first",
  col_prefix: "score_"
}
```

### Output

```json
[
  {"student_id": 1, "score_Reading": 85, "score_Writing": 90},
  {"student_id": 2, "score_Reading": 75, "score_Writing": null}
]
```

## Supported Aggregators

- `first`: First value
- `last`: Last value
- `max`: Maximum value
- `min`: Minimum value
- `mean`: Mean value
- `count`: Count of values

## Related Transforms

- [Group By](./group-by.md): Alternative aggregation approach

