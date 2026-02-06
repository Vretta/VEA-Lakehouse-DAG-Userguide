# Merge Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/merge.ts`

## Overview

The `merge` transform performs SQL-like joins between two dataframes. It supports inner joins and left joins using a sort-merge join algorithm.

## Function Signatures

```typescript
export function inner_join(
  leftIn: IRow[], 
  rightIn: IRow[], 
  left_on: string[], 
  right_on?: string[]
): IRow[]

export function left_join(
  leftIn: IRowNullable[], 
  rightIn: IRowNullable[], 
  left_on: string[], 
  right_on?: string[]
): IRowNullable[]
```

## Join Types

### Inner Join
Returns only rows where there is a match in both dataframes.

### Left Join
Returns all rows from the left dataframe, with matching rows from the right dataframe. If no match, right columns are null.

## Parameters

- `leftIn`: Left dataframe
- `rightIn`: Right dataframe
- `left_on`: Column(s) from left dataframe to join on
- `right_on`: Column(s) from right dataframe to join on (defaults to `left_on` if not provided)

## Algorithm

Uses **sort-merge join** algorithm:
1. Creates sorted indices on join keys for both dataframes
2. Merges the sorted indices to find matching rows
3. Combines matching rows into result

## Column Handling

- Join keys from right dataframe are removed from output
- If columns have the same name (excluding join keys), right columns are renamed with `_right` suffix
- All other columns from both dataframes are included

## Example

### Input Data

**Left DataFrame:**
```json
[
  {"student_id": 1, "name": "Alice", "school_id": 10},
  {"student_id": 2, "name": "Bob", "school_id": 10},
  {"student_id": 3, "name": "Charlie", "school_id": 20}
]
```

**Right DataFrame:**
```json
[
  {"school_id": 10, "school_name": "School A", "district": "District 1"},
  {"school_id": 20, "school_name": "School B", "district": "District 2"}
]
```

### Inner Join Configuration

```typescript
{
  how: "inner",
  left: "students",
  right: "schools",
  left_on: ["school_id"],
  right_on: ["school_id"]
}
```

### Output (Inner Join)

```json
[
  {"student_id": 1, "name": "Alice", "school_id": 10, "school_name": "School A", "district": "District 1"},
  {"student_id": 2, "name": "Bob", "school_id": 10, "school_name": "School A", "district": "District 1"},
  {"student_id": 3, "name": "Charlie", "school_id": 20, "school_name": "School B", "district": "District 2"}
]
```

### Left Join Configuration

```typescript
{
  how: "left",
  left: "students",
  right: "schools",
  left_on: ["school_id"],
  right_on: ["school_id"]
}
```

### Output (Left Join)

Same as inner join in this example, but would include all left rows even if no match in right.

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "join",
    how: "inner",
    left: "students",
    right: "schools",
    left_on: ["school_id"],
    right_on: ["school_id"]
  },
  dependencySourcings: [
    {
      param: "students",
      type: "dataframe",
      config: { asset: "load_students" }
    },
    {
      param: "schools",
      type: "dataframe",
      config: { asset: "load_schools" }
    }
  ]
}
```

## Multiple Join Keys

Join can be performed on multiple columns:

```typescript
{
  left_on: ["school_id", "grade"],
  right_on: ["school_id", "grade_level"]
}
```

## Notes

- Currently only sort-merge join is implemented
- Hash-based joins are planned for small tables (TODO)
- Null values in join keys are handled (may affect matching)
- Empty dataframes return empty results
- Join keys must have the same number of columns on both sides

## Performance Considerations

- Sort-merge join is efficient for large datasets
- Both dataframes are sorted before joining
- Consider filtering dataframes before joining to improve performance

## Related Transforms

- [Concat](./concat.md): Concatenate dataframes vertically
- [Replace Where](./replace-where.md): Conditional value replacement

