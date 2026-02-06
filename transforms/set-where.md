# Set Where Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/set-where.ts`

## Overview

The `set-where` transform conditionally sets column values based on predicate conditions. Supports single conditions or complex condition combinations with AND/OR logic.

## Function Signatures

```typescript
export function setWhere(
  df_input: IRow[], 
  values: {[key: string]: Value}, 
  values_false?: {[key: string]: Value}, 
  condition?: Condition, 
  conditions?: {parts: Condition[], join: string}, 
  config_props?: any
): IRow[]

export function setWhereInplace(
  df_input: IRow[], 
  values: {[key: string]: Value}, 
  values_false?: {[key: string]: Value}, 
  condition?: Condition, 
  conditions?: {parts: Condition[], join: string}, 
  config_props?: any
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `values`: Object mapping column names to values (applied when condition is true)
- `values_false`: Optional object mapping column names to values (applied when condition is false)
- `condition`: Single condition object
- `conditions`: Multiple conditions with join operator ("and" or "or")
- `config_props`: Configuration properties for dynamic value sourcing

## Condition Structure

```typescript
type Condition = {
  comparison: string,      // Comparison operator
  col?: string,            // Column to compare
  value_col?: string,      // Column to use as comparison value
  type?: string,           // Type hint
  config_prop?: string,    // Config property to use as value
  value: NonNullValue,     // Static value to compare
}
```

## Supported Comparisons

- `equals`: Column equals value
- `not-equal`: Column does not equal value
- `greater-than`: Column > value
- `less-than`: Column < value
- `greater-than-equal`: Column >= value
- `less-than-equal`: Column <= value
- `in`: Column value in array
- `not-in`: Column value not in array
- `non-null`: Column is not null
- `null`: Column is null
- `is-empty-string`: Column is empty string
- `not-empty-string`: Column is not empty string

## Example

### Input Data

```json
[
  {"student_id": 1, "score": 85, "status": "pending"},
  {"student_id": 2, "score": 60, "status": "pending"},
  {"student_id": 3, "score": 90, "status": "pending"}
]
```

### Single Condition

```typescript
{
  df_input: "students",
  condition: {
    comparison: "greater-than-equal",
    col: "score",
    value: 70
  },
  values: {
    "status": "pass"
  },
  values_false: {
    "status": "fail"
  }
}
```

### Output

```json
[
  {"student_id": 1, "score": 85, "status": "pass"},
  {"student_id": 2, "score": 60, "status": "fail"},
  {"student_id": 3, "score": 90, "status": "pass"}
]
```

### Multiple Conditions (AND)

```typescript
{
  df_input: "students",
  conditions: {
    parts: [
      {
        comparison: "greater-than",
        col: "score",
        value: 60
      },
      {
        comparison: "less-than",
        col: "score",
        value: 90
      }
    ],
    join: "and"
  },
  values: {
    "status": "moderate"
  }
}
```

### Multiple Conditions (OR)

```typescript
{
  df_input: "students",
  conditions: {
    parts: [
      {
        comparison: "equals",
        col: "grade",
        value: 3
      },
      {
        comparison: "equals",
        col: "grade",
        value: 4
      }
    ],
    join: "or"
  },
  values: {
    "is_elementary": 1
  }
}
```

### Using Config Properties

```typescript
{
  df_input: "students",
  condition: {
    comparison: "greater-than",
    col: "score",
    config_prop: "passing_threshold"
  },
  values: {
    "status": "pass"
  }
}
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "set-where",
    df_input: "students",
    condition: {
      comparison: "greater-than-equal",
      col: "score",
      value: 70
    },
    values: {
      "passing": 1
    },
    values_false: {
      "passing": 0
    }
  }
}
```

## Inplace vs Non-Inplace

- `setWhere`: Creates new rows (immutable)
- `setWhereInplace`: Modifies rows in place (mutable, more memory efficient)

## Notes

- Multiple columns can be set in a single operation via `values` object
- `values_false` is optional - if not provided, rows not matching condition are unchanged
- Conditions can reference config properties for dynamic values
- Column-to-column comparisons supported via `value_col`

## Related Transforms

- [Replace Where](./replace-where.md): Replace values from another dataframe
- [Filter Col](./filter-col.md): Filter rows based on conditions
- [Map Col](./map-col.md): Create new columns with operations

