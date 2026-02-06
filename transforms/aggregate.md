# Aggregate Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/aggregate.ts`

## Overview

The `aggregate` transform performs aggregations on an entire dataframe, producing a single row with aggregated values. This is useful for computing summary statistics across all records.

## Function Signature

```typescript
export function aggregate(df_input: IRow[], agg: IRow[]): IRow[]
```

## Parameters

- `df_input`: Input dataframe (array of records)
- `agg`: Array of aggregation definitions

## Aggregation Definition

Each aggregation definition has the following structure:

```typescript
{
  col_new: string,        // Name of the output column
  agg_type: string,       // Type of aggregation
  col_target: string      // Column to aggregate
}
```

## Supported Aggregation Types

### Numeric Aggregations

- `sum`: Sum of all values
- `mean`: Mean (average) of all values
- `min`: Minimum value
- `max`: Maximum value
- `variance`: Variance (degrees of freedom = 1)
- `stdev`: Standard deviation (unbiased estimator)
- `count`: Number of values (length of array)

### Set Aggregations

- `nunique`: Number of unique values
- `list_unique`: Array of unique values

### String Aggregations

- `concat`: Concatenate all values with semicolon separator

### Date Aggregations

- `minDate`: Earliest date
- `maxDate`: Latest date

## Example

### Input Data

```json
[
  {"student_id": 1, "score": 85},
  {"student_id": 2, "score": 90},
  {"student_id": 3, "score": 75}
]
```

### Configuration

```typescript
{
  df_input: "students",
  agg: [
    {
      col_new: "total_students",
      agg_type: "count",
      col_target: "student_id"
    },
    {
      col_new: "avg_score",
      agg_type: "mean",
      col_target: "score"
    },
    {
      col_new: "min_score",
      agg_type: "min",
      col_target: "score"
    },
    {
      col_new: "max_score",
      agg_type: "max",
      col_target: "score"
    }
  ]
}
```

### Output

```json
[
  {
    "total_students": 3,
    "avg_score": 83.33,
    "min_score": 75,
    "max_score": 90
  }
]
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "aggregate",
    df_input: "students",
    agg: [
      {
        col_new: "total_count",
        agg_type: "count",
        col_target: "student_id"
      }
    ]
  }
}
```

## Notes

- Returns empty array `[]` if input dataframe is empty
- All aggregations are computed and returned in a single row
- For `count` aggregation, `col_target` is not required but can be any column
- Date aggregations filter out invalid dates before processing

## Related Transforms

- [Group By](./group-by.md): Similar aggregations but grouped by columns
- [Aggregators](./aggregators.md): Core aggregation functions

