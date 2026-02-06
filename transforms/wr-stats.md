# WR Stats Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/wr-stats.ts`

## Overview

The `wr-stats` transform computes Winsorized Robust (WR) statistics on a column.

## Function Signature

```typescript
export function wr_stats(
  df_input: IRow[], 
  column: string
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `column`: Column to compute statistics on

## Usage

Computes robust statistical measures using Winsorization technique to handle outliers.

## Related Transforms

- [Correlations](./correlations.md): Other statistical computations

