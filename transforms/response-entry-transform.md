# Response Entry Expand Transform

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/transforms/json-expand-array.ts`

## Overview

The `response-entry-expand` transform expands `response_raw` JSON objects into multiple rows, creating one output row per entry ID. This transform is specifically designed for the ABED response data structure where `response_raw` contains an object with entry IDs as keys and a `__meta` field for ordering.

**⚠️ Availability Note**: This transform is currently only available in the ABED branch. It is not available in other assessment platforms at this time.

## Response Raw Structure

The `response_raw` field stores a JSON object with the following structure:

```json
{
  "__meta": {
    "entryOrder": [0, 1, 2, ...]
  },
  "0": {
    "value": "response text",
    "type": "WR",
    ...
  },
  "1": {
    "value": "another response",
    "type": "WR",
    ...
  }
}
```

- `__meta.entryOrder`: Array of entry IDs in the order they should be processed
- Entry IDs (e.g., "0", "1", "2"): Keys containing entry data objects
- Entry data objects: Contain `value`, `type`, and other response-specific fields

## Function Signature

```typescript
export function responseEntryExpand(
  df_input: IRow[],
  sourceColumn?: string,
  entryIdColumn?: string,
  entryRawColumn?: string,
  entryOrderColumn?: string,
  responseTypeColumn?: string
): IRow[]
```

## Parameters

- `df_input`: Input dataframe
- `sourceColumn`: Column name containing `response_raw` JSON (default: "response_raw")
- `entryIdColumn`: Output column name for entry ID (default: "entry_id")
- `entryRawColumn`: Output column name for entry raw value (default: "response_raw")
- `entryOrderColumn`: Output column name for entry order (default: "entry_order")
- `responseTypeColumn`: Output column name for response type (default: "response_type")

## Usage

Expands `response_raw` JSON objects into multiple rows, where each entry ID becomes a separate row with its entry ID, value, order, and type.

## Behavior

1. **JSON Object Parsing**: Parses `response_raw` as a JSON object (not an array)
2. **Entry ID Extraction**: Extracts entry IDs from object keys, excluding `__meta` and `undefined`
3. **Ordering**: Uses `__meta.entryOrder` for ordering if available, otherwise sorts numerically
4. **Value Extraction**: Extracts response value from `entryData.value`, `entryData.str`, `entryData.raw`, or falls back to JSON stringification
5. **Type Extraction**: Extracts `response_type` from `entryData.type`
6. **Empty/Null Handling**: Null or empty `response_raw` creates a single row with null entry metadata
7. **Malformed JSON**: Malformed JSON strings are preserved as-is (single row with original value)

## Example

### Basic Usage

```typescript
{
  "kind": "response-entry-expand",
  "df_output": "raw_responses_by_entry",
  "config": {
    "df_input": "raw_responses",
    "column": "response_raw",
    "entry_id_column": "entry_id",
    "entry_raw_column": "response_raw",
    "entry_order_column": "entry_order",
    "response_type_column": "response_type"
  }
}
```

### Input Data

```json
[
  {
    "test_attempt_id": 1,
    "item_id": 10,
    "response_raw": "{\"__meta\":{\"entryOrder\":[0,1]},\"0\":{\"value\":\"part1\",\"type\":\"WR\"},\"1\":{\"value\":\"part2\",\"type\":\"WR\"}}"
  }
]
```

### Output Data

```json
[
  {
    "test_attempt_id": 1,
    "item_id": 10,
    "entry_id": "0",
    "response_raw": "part1",
    "entry_order": 0,
    "response_type": "WR"
  },
  {
    "test_attempt_id": 1,
    "item_id": 10,
    "entry_id": "1",
    "response_raw": "part2",
    "entry_order": 1,
    "response_type": "WR"
  }
]
```

## Use Cases

1. **Multi-Entry Response Breakdown**: Breaking down WR responses with multiple components into individual entries
2. **Entry-Level Analysis**: Enabling analysis of individual response entries for items with multiple parts
3. **Entry Metadata Extraction**: Extracting entry-specific metadata (like "Quel-type de texte" for French Grade 6 PAT)

## Notes

- The original source column is removed from output rows
- All other columns from the input dataframe are preserved in each expanded row
- Entry IDs are preserved as strings (as they appear in the JSON keys)
- Uses `__meta.entryOrder` for correct ordering when available
- Handles missing `__meta.entryOrder` by falling back to numeric sorting
- Excludes `__meta` and `undefined` keys from entry processing

## Related Transforms

- [Extract Columns](./extract-cols.md): Extract nested properties from JSON objects
- [Restrict Cols](./restrict-cols.md): Select specific columns after expansion
