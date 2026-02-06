# Map Column Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/map-col.ts`

## Overview

The `map-col` transform creates a new column by applying an operation to one or more source columns. It supports arithmetic, comparison, string, and conversion operations.

## Function Signature

```typescript
export function map_col(
  df_input: IRow[], 
  col_output: string, 
  source_cols: string[], 
  operation: string, 
  options: {[key: string]: any}
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `col_output`: Name of the output column to create
- `source_cols`: Array of source column names (1-2 columns depending on operation)
- `operation`: Operation to perform
- `options`: Operation-specific options

## Supported Operations

### Conversion Operations

- `convert-bool`: Convert value to boolean (uses `value_true`/`value_false` options)
- `to-number`: Convert value to number

### Comparison Operations (2 columns)

- `equal`: Check if two values are equal
- `not-equal`: Check if two values are not equal
- `greater-than`: Check if first value > second value
- `less-than`: Check if first value < second value
- `greater-than-equal`: Check if first value >= second value
- `less-than-equal`: Check if first value <= second value

**Options:**
- `value_true`: Value to return if comparison is true
- `value_false`: Value to return if comparison is false

### Arithmetic Operations (2 columns)

- `add`: Add two values
- `subtract`: Subtract second from first
- `divide`: Divide first by second
- `percentage`: Calculate percentage (first / second * 100)

### String Operations

- `concat`: Concatenate values with optional delimiter

**Options:**
- `delimiter`: String to use as separator (default: empty string)

### Statistical Operations

- `dp-std-err`: Calculate standard error (for decimal places)

## Example

### Input Data

```json
[
  {"score": 85, "max_score": 100, "student_name": "Alice", "last_name": "Smith"},
  {"score": 90, "max_score": 100, "student_name": "Bob", "last_name": "Jones"}
]
```

### Configuration 1: Percentage Calculation

```typescript
{
  df_input: "students",
  col_output: "percentage",
  source_cols: ["score", "max_score"],
  operation: "percentage"
}
```

### Output 1

```json
[
  {"score": 85, "max_score": 100, "percentage": 85, "student_name": "Alice", "last_name": "Smith"},
  {"score": 90, "max_score": 100, "percentage": 90, "student_name": "Bob", "last_name": "Jones"}
]
```

### Configuration 2: String Concatenation

```typescript
{
  df_input: "students",
  col_output: "full_name",
  source_cols: ["student_name", "last_name"],
  operation: "concat",
  options: {
    delimiter: " "
  }
}
```

### Output 2

```json
[
  {"score": 85, "max_score": 100, "full_name": "Alice Smith", ...},
  {"score": 90, "max_score": 100, "full_name": "Bob Jones", ...}
]
```

### Configuration 3: Comparison with Custom Values

```typescript
{
  df_input: "students",
  col_output: "is_passing",
  source_cols: ["score", "max_score"],
  operation: "greater-than-equal",
  options: {
    value_true: "PASS",
    value_false: "FAIL"
  }
}
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "map-col",
    df_input: "attempts",
    col_output: "passing_status",
    source_cols: ["score", "passing_threshold"],
    operation: "greater-than-equal",
    options: {
      value_true: 1,
      value_false: 0
    }
  }
}
```

## Operation Arity

Operations require a specific number of source columns:

- **1 column**: `convert-bool`, `to-number`
- **2 columns**: All comparison and arithmetic operations
- **Variable**: `concat`, `dp-std-err` (can use multiple columns)

## Error Handling

- Throws error if operation is not supported
- Throws error if number of source columns doesn't match operation arity
- Null values are handled appropriately for each operation type

## Notes

- Original columns are preserved in output
- New column is added to each row
- Operations are applied row-by-row
- For division, division by zero may result in Infinity or NaN
- String operations convert all values to strings

## Related Transforms

- [Set Where](./set-where.md): Conditional value setting
- [Replace Where](./replace-where.md): Conditional value replacement
- [Cast Col](./cast-col.md): Type conversion

