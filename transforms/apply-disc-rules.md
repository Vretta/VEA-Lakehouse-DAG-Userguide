# Apply Discontinuation Rules Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/apply-disc-rules.ts`

## Overview

The `apply-disc-rules` transform applies discontinuation rules to assessment data, typically used to determine if a student's assessment should be discontinued based on domain-level performance.

## Function Signature

```typescript
export function apply_disc_rules(
  df_input: IRow[], 
  group_by: string[], 
  disc_rules: any[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe with domain-level data
- `group_by`: Columns to group by (typically student/attempt identifiers)
- `disc_rules`: Array of discontinuation rule definitions

## Usage

Applied to domain-level data to determine overall discontinuation status based on rules that evaluate multiple domains.

## Related Transforms

- [Group By](./group-by.md): Often used before applying disc rules
- [Priority All Or Nothing](./priority-all-or-nothing.md): Similar priority-based logic

