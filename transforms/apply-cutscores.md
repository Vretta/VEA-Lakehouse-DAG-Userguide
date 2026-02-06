# Apply Cutscores Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/apply-cutscores.ts`

## Overview

The `apply-cutscores` transform applies cutscore definitions to assign labels based on score values. Cutscores define thresholds that categorize scores into performance levels.

## Function Signature

```typescript
export function applyCutscores(
  df_input: IRow[], 
  groupKey: string[], 
  scoreColumn: string, 
  cuts: any[], 
  labelColumn: string
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `groupKey`: Columns to group cutscores by (e.g., ["asmt_code", "form_code"])
- `scoreColumn`: Column containing scores to evaluate
- `cuts`: Array of cutscore definitions
- `labelColumn`: Output column name for assigned labels

## Cutscore Definition

Each cutscore definition has the structure:

```typescript
{
  // Group key values (must match groupKey columns)
  "asmt_code": "MATH",
  "form_code": "A",
  
  // Cutscore definition
  "label": "Level 3",
  "cutoff": 70.5,
  "inclusive": true  // true for <=, false for <
}
```

## Special Cases

- **Null scores**: Do not get a label (set to null)
- **NR (No Response)**: If `is_nr == 1`, label is set to 'NA'
- **No match**: If no cutscore matches, label is set to null

## Example

### Input Data

```json
[
  {"student_id": 1, "asmt_code": "MATH", "form_code": "A", "score": 85, "is_nr": 0},
  {"student_id": 2, "asmt_code": "MATH", "form_code": "A", "score": 65, "is_nr": 0},
  {"student_id": 3, "asmt_code": "MATH", "form_code": "A", "score": null, "is_nr": 1}
]
```

### Cutscores

```json
[
  {
    "asmt_code": "MATH",
    "form_code": "A",
    "label": "Level 1",
    "cutoff": 50,
    "inclusive": true
  },
  {
    "asmt_code": "MATH",
    "form_code": "A",
    "label": "Level 2",
    "cutoff": 70,
    "inclusive": true
  },
  {
    "asmt_code": "MATH",
    "form_code": "A",
    "label": "Level 3",
    "cutoff": 85,
    "inclusive": true
  }
]
```

### Configuration

```typescript
{
  df_input: "students",
  group_by: ["asmt_code", "form_code"],
  cut_key: ["asmt_code", "form_code"],
  score_column: "score",
  cutscore_defs: "cutscores",
  label_column: "performance_level"
}
```

### Output

```json
[
  {"student_id": 1, "asmt_code": "MATH", "form_code": "A", "score": 85, "performance_level": "Level 3", "is_nr": 0},
  {"student_id": 2, "asmt_code": "MATH", "form_code": "A", "score": 65, "performance_level": "Level 2", "is_nr": 0},
  {"student_id": 3, "asmt_code": "MATH", "form_code": "A", "score": null, "performance_level": "NA", "is_nr": 1}
]
```

## Usage in Asset Definition

```typescript
{
  method: "transform",
  methodConfig: {
    transformSlug: "apply-cutscores",
    df_input: "students",
    group_by: ["asmt_code", "form_code"],
    cut_key: ["asmt_code", "form_code"],
    score_column: "score",
    cutscore_defs: "cutscores",
    label_column: "performance_level"
  },
  dependencySourcings: [
    {
      param: "students",
      type: "dataframe",
      config: { asset: "load_students" }
    },
    {
      param: "cutscores",
      type: "dataframe",
      config: { asset: "load_cutscores" }
    }
  ]
}
```

## Algorithm

1. For each row, find matching cutscores based on groupKey
2. Sort matching cutscores by cutoff value (lowest to highest)
3. Evaluate score against cutscores in order
4. Assign first matching label (based on inclusive flag)
5. Handle special cases (null, NR)

## Notes

- Cutscores are sorted by cutoff before evaluation
- First matching cutscore is applied (stops at first match)
- Group key matching is exact (all groupKey columns must match)
- EYS-specific logic for NR handling is included

## Related Transforms

- [Cutoffs](./cutoffs.md): Generate cutscore definitions
- [Set Where](./set-where.md): Conditional value setting

