# Normed Histogram Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/normed-histogram.ts`

## Overview

The `normed-histogram` transform creates a normalized histogram from statistical data (count, mean, stdev).

## Function Signature

```typescript
export function normed_histogram(
  df_input: IRow[], 
  n_bins: number
): IRow[]
```

## Parameters

- `df_input`: Input dataframe with count, mean, stdev columns
- `n_bins`: Number of bins for histogram

## Usage

Creates histogram bins from statistical summary data.

## Related Transforms

- [Group By](./group-by.md): Often used to generate input statistics

