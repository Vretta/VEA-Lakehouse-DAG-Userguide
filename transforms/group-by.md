# Group By Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/group-by.ts`

## Overview

The `group-by` transform groups rows by specified columns and applies aggregations to each group, similar to SQL's GROUP BY clause.

## Function Signature

```typescript
export function group_by(
  df_input: IRow[], 
  columns: string[], 
  agg: IRow[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe (array of records)
- `columns`: Array of column names to group by
- `agg`: Array of aggregation definitions

## Aggregation Definition

Each aggregation definition has the following structure:

```typescript
{
  col_new: string,        // Name of the output column
  agg_type: string,       // Type of aggregation
  col_target: string,     // Column to aggregate
  // Special aggregation options:
  impute_na_domains?: string[],  // For overall-plus-one
  comparison_col?: string,      // For overall-plus-one
  domain_col?: string,           // For overall-plus-one
  priority_domain?: string,      // For overall-plus-one
  priority_comparison?: string,   // For overall-plus-one
  plus_one_comparison?: string,   // For overall-plus-one
  check_any_na?: boolean,         // For overall-plus-one
  priorities?: string[]           // For priority-all-or-nothing
}
```

## Standard Aggregation Types

### Numeric Aggregations
- `sum`: Sum of values
- `mean`: Mean (average) of values
- `min`: Minimum value
- `max`: Maximum value
- `variance`: Variance
- `stdev`: Standard deviation
- `count`: Number of records in group

### Set Aggregations
- `nunique`: Number of unique values
- `list_unique`: Array of unique values

### String Aggregations
- `concat`: Concatenate values with semicolon
- `concat-arr`: Array of all values

### Date Aggregations
- `minDate`: Earliest date
- `maxDate`: Latest date

### Special Aggregations
- `first`: First value in group
- `quantile-17_5`: 17.5th percentile
- `quantile-82_5`: 82.5th percentile

## Special Aggregation Types

### overall-plus-one

Complex aggregation logic for assessment outcomes. Used for determining overall outcomes based on domain-level outcomes with priority rules.

**Configuration:**
```typescript
{
  col_new: "overall_outcome",
  agg_type: "overall-plus-one",
  comparison_col: "outcome",
  domain_col: "domain",
  priority_domain: "LENS",
  priority_comparison: "NRAS",
  plus_one_comparison: "RAS",
  check_any_na: true,
  impute_na_domains: ["CC1", "CC2", "CC3"]
}
```

**Logic:**
1. If any NA in comparison_col when `check_any_na` is true, result is NA
2. If priority domain not found, result is NA
3. If priority domain equals priority_comparison, result is priority_comparison
4. If priority domain equals plus_one_comparison and another domain also equals plus_one_comparison, result is plus_one_comparison
5. If priority domain equals plus_one_comparison and all others equal priority_comparison, result is priority_comparison
6. Otherwise, result is NA

### priority-all-or-nothing

Determines outcome based on priority order.

**Configuration:**
```typescript
{
  col_new: "final_outcome",
  agg_type: "priority-all-or-nothing",
  comparison_col: "outcome",
  priorities: ["RAS_ERR", "NA", "RAS", "NRAS"]
}
```

**Logic:**
- Iterates through priorities in order
- Returns first matching outcome found in group
- Also sets `score` column from domain_label='time' row if found

## Example

### Input Data

```json
[
  {"school_id": 1, "grade": 3, "score": 85},
  {"school_id": 1, "grade": 3, "score": 90},
  {"school_id": 1, "grade": 4, "score": 75},
  {"school_id": 2, "grade": 3, "score": 80}
]
```

### Configuration

```typescript
{
  df_input: "students",
  group_by: ["school_id", "grade"],
  agg: [
    {
      col_new: "avg_score",
      agg_type: "mean",
      col_target: "score"
    },
    {
      col_new: "student_count",
      agg_type: "count",
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
    "school_id": 1,
    "grade": 3,
    "avg_score": 87.5,
    "student_count": 2,
    "min_score": 85,
    "max_score": 90
  },
  {
    "school_id": 1,
    "grade": 4,
    "avg_score": 75,
    "student_count": 1,
    "min_score": 75,
    "max_score": 75
  },
  {
    "school_id": 2,
    "grade": 3,
    "avg_score": 80,
    "student_count": 1,
    "min_score": 80,
    "max_score": 80
  }
]
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "group-by",
    df_input: "attempts",
    group_by: ["school_id", "form_code"],
    agg: [
      {
        col_new: "total_attempts",
        agg_type: "count",
        col_target: "attempt_id"
      },
      {
        col_new: "avg_score",
        agg_type: "mean",
        col_target: "score"
      }
    ]
  }
}
```

## Notes

- Group keys are created by joining column values with `|` separator
- Multiple aggregations can be applied to the same group
- Special aggregations (`overall-plus-one`, `priority-all-or-nothing`) have domain-specific logic
- Empty groups are not included in output
- Null values in group-by columns create separate groups

## Related Transforms

- [Aggregate](./aggregate.md): Aggregations without grouping
- [Group By Plus One](./group-by-plus-one.md): Similar but with additional overall aggregation
- [Pivot](./pivot.md): Reshape data with grouping

