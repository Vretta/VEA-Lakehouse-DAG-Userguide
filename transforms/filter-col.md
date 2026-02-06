# Filter Column Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/filter-col.ts`

## Overview

The `filter-col` transform filters rows from a dataframe based on conditions applied to a column. All filters **keep** the record if the comparison is TRUE.

## Function Signature

```typescript
export function filter_col(
  df_input: IRow[], 
  column: string, 
  comparison: string, 
  value?: any
): IRow[]
```

## Parameters

- `df_input`: Input dataframe (array of records)
- `column`: Column name to filter on
- `comparison`: Comparison operator
- `value`: Value(s) to compare against (optional for some comparisons)

## Supported Comparisons

### Membership Comparisons

- `in`: Column value is in the provided array
- `not-in`: Column value is not in the provided array

**Example:**
```typescript
filter_col(df, "school_id", "in", [1, 2, 3])
```

### Equality Comparisons

- `equals`: Column value equals the provided value
- `not-equal` (or `not-equals`): Column value does not equal the provided value

**Example:**
```typescript
filter_col(df, "status", "equals", "active")
filter_col(df, "status", "not-equal", "inactive")
```

### Numeric Comparisons

- `greater-than`: Column value is greater than the provided value
- `greater-than-equal`: Column value is greater than or equal to the provided value
- `less-than`: Column value is less than the provided value
- `less-than-equal`: Column value is less than or equal to the provided value

**Note:** Null values are excluded from numeric comparisons.

**Example:**
```typescript
filter_col(df, "score", "greater-than", 70)
```

### Null Comparisons

- `non-null` (or `not-null`): Column value is not null or undefined
- `null` (or `is-null`): Column value is null or undefined

**Example:**
```typescript
filter_col(df, "email", "non-null")
// or equivalently:
filter_col(df, "email", "not-null")
```

### String Comparisons

- `is-empty-string`: Column value is empty string, null, or undefined (after trimming)
- `not-empty-string`: Column value is not empty (has content after trimming)

**Example:**
```typescript
filter_col(df, "notes", "not-empty-string")
```

## Example

### Input Data

```json
[
  {"student_id": 1, "score": 85, "status": "active"},
  {"student_id": 2, "score": 60, "status": "inactive"},
  {"student_id": 3, "score": 90, "status": "active"},
  {"student_id": 4, "score": null, "status": "active"}
]
```

### Configuration

```typescript
{
  df_input: "students",
  col: "score",
  comparison: "greater-than",
  value: 70
}
```

### Output

```json
[
  {"student_id": 1, "score": 85, "status": "active"},
  {"student_id": 3, "score": 90, "status": "active"}
]
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "filter-col",
    df_input: "students",
    col: "school_id",
    comparison: "in",
    value_src: {
      df: "active_schools",
      col: "school_id"
    }
  }
}
```

## Dynamic Value Sourcing

The filter can source values dynamically from other assets:

```typescript
{
  col: "student_id",
  comparison: "in",
  value_src: "active_student_ids"  // Asset column reference
}
```

Or from another dataframe:

```typescript
{
  col: "school_id",
  comparison: "in",
  value_src: {
    df: "filtered_schools",
    col: "school_id"
  }
}
```

## Error Handling

- Throws error if column does not exist in dataframe
- Throws error for unsupported comparison operators
- Null values are handled appropriately for each comparison type

## Notes

- All comparisons are case-sensitive for string values
- For `in` and `not-in`, values are converted to strings for comparison
- Numeric comparisons exclude null values automatically
- Empty string checks trim whitespace before evaluation

## Related Transforms

- [Set Where](./set-where.md): Set values based on conditions
- [Replace Where](./replace-where.md): Replace values based on conditions

