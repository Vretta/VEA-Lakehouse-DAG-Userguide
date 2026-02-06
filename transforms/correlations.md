# Correlations Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/correlations.ts`

## Overview

The `correlations` transform computes correlation coefficients, specifically point-biserial correlation for binary variables.

## Function Signature

```typescript
export function pointBiserial(
  df_input: IRow[], 
  keep_cols?: string[]
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `keep_cols`: Optional columns to keep in output

## Usage

Computes point-biserial correlation between binary and continuous variables.

## Related Transforms

- [WR Stats](./wr-stats.md): Other statistical computations

