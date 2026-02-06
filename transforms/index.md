# Transform Functions

Transform functions are pure data transformation operations that process dataframes within the DAG pipeline. Each transform is a self-contained operation that takes one or more dataframes as input and produces a dataframe as output.

## Valid Transform Kinds

> **IMPORTANT**: Only the transforms listed below are implemented and valid. Using an invalid transform kind will cause a runtime error: `"Transform [kind] does not exist in step [n]"`

The following transform kinds are available (defined in `data-export-queries/services/_index.ts`):

| Kind | Description | Documentation |
|------|-------------|---------------|
| `join` | Performs SQL-like joins between two dataframes | [Merge](./merge.md) |
| `filter-col` | Filters rows based on column conditions | [Filter Column](./filter-col.md) |
| `drop-duplicates` | Removes duplicate rows | [Drop Duplicates](./drop-duplicates.md) |
| `group-by` | Groups rows and applies aggregations | [Group By](./group-by.md) |
| `map-col` | Creates new columns from source columns | [Map Column](./map-col.md) |
| `restrict-cols` | Selects only specified columns | [Restrict Columns](./restrict-cols.md) |
| `sort-by` | Sorts rows by columns | [Sort By](./sort-by.md) |
| `aggregate` | Performs aggregations on entire dataframe | [Aggregate](./aggregate.md) |
| `normed-histogram` | Creates normalized histogram | [Normed Histogram](./normed-histogram.md) |
| `replace-where` | Replaces values based on matching keys | [Replace Where](./replace-where.md) |
| `rename-cols` | Renames columns | [Rename Columns](./rename-cols.md) |
| `concat` | Concatenates dataframes vertically | [Concat](./concat.md) |
| `fill-na` | Fills null values with defaults | [Fill NA](./fill-na.md) |
| `cutoffs` | Generates cutscore definitions | [Cutoffs](./cutoffs.md) |
| `apply-cutscores` | Applies cutscore labels | [Apply Cutscores](./apply-cutscores.md) |
| `set-where` | Conditionally sets values | [Set Where](./set-where.md) |
| `point-biserial` | Computes correlation coefficients | [Correlations](./correlations.md) |
| `pivot` | Reshapes long to wide format | [Pivot](./pivot.md) |
| `wr-stats` | Computes Winsorized Robust statistics | [WR Stats](./wr-stats.md) |
| `cast-col` | Converts column types | [Cast Column](./cast-col.md) |
| `overall-plus-one` | Group with overall aggregation | [Group By Plus One](./group-by-plus-one.md) |
| `priority-all-or-nothing` | Returns first matching priority value | [Priority All Or Nothing](./priority-all-or-nothing.md) |
| `apply-disc-rules` | Applies discontinuation rules | [Apply Disc Rules](./apply-disc-rules.md) |
| `extract-cols` | Extracts nested JSON properties | [Extract Columns](./extract-cols.md) |
| `round-number` | Rounds numeric values | [Round Number](./round-number.md) |

### Common Mistake: Placeholder Transforms

Do **NOT** use fake transform kinds like `placeholder`, `passthrough`, `copy`, or `identity` - they do not exist. If you need to pass through data unchanged:

```typescript
// ❌ WRONG - 'placeholder' is not a valid transform
{
    kind: "placeholder",
    df_output: "result",
    config: { df_input: "source_df" }
}

// ✅ CORRECT - Use restrict-cols with all columns
{
    kind: "restrict-cols",
    df_output: "result",
    config: {
        df_input: "source_df",
        cols: ["col1", "col2", "col3"]  // List all columns to pass through
    }
}
```

## Categorization

Transforms are organized into five categories based on their primary function:

### I. Structural & Schema Transforms

*Operations that change the shape, columns, or data types of the dataframe.*

- **[Cast Column](./cast-col.md)** - Converts a column to a different data type (number, string, boolean, date)
- **[Concat](./concat.md)** - Concatenates multiple dataframes vertically (row-wise combination)
- **[Extract Columns](./extract-cols.md)** - Extracts nested properties from JSON columns into separate flat columns
- **[Response Entry Expand](./response-entry-transform.md)** - Expands response_raw JSON object into multiple rows, one per entry ID ⚠️ *ABED branch only*
- **[Merge](./merge.md)** - Performs SQL-like joins between two dataframes (inner join, left join)
- **[Pivot](./pivot.md)** - Reshapes data from long to wide format, creating columns from values
- **[Rename Columns](./rename-cols.md)** - Renames columns in a dataframe
- **[Restrict Columns](./restrict-cols.md)** - Selects only specified columns from a dataframe
- **[Sort By](./sort-by.md)** - Sorts rows in dataframe by specified columns (ascending/descending)

### II. Data Cleaning & Integrity

*Operations focused on data "hygiene" and handling missingness.*

- **[Drop Duplicates](./drop-duplicates.md)** - Removes duplicate rows based on specified columns
- **[Fill NA](./fill-na.md)** - Fills null/undefined values in specified columns with default values
- **[Filter Column](./filter-col.md)** - Filters rows based on conditions applied to a column (in, equals, greater-than, etc.)
- **[Label Duplicates](./label-duplicates.md)** - Identifies and labels duplicate rows instead of removing them

### III. Logic & Value Mapping

*Operations that compute new values or modify existing ones based on business logic.*

- **[Map Column](./map-col.md)** - Creates new column by applying operations to source columns (arithmetic, comparison, string ops)
- **[Priority All Or Nothing](./priority-all-or-nothing.md)** - Determines outcome based on priority order, returning first matching value
- **[Replace Where](./replace-where.md)** - Replaces values in target dataframe with values from source based on matching keys
- **[Round Number](./round-number.md)** - Rounds numeric values in specified columns to given precision
- **[Set Where](./set-where.md)** - Conditionally sets column values based on predicate conditions (supports AND/OR logic)

### IV. Aggregation & Summarization (Descriptive)

*Operations that reduce many rows into fewer rows or summary statistics.*

- **[Aggregate](./aggregate.md)** - Performs aggregations on entire dataframe (sum, mean, count, etc.)
- **[Group By](./group-by.md)** - Groups rows by specified columns and applies aggregations to each group
- **[Group By Plus One](./group-by-plus-one.md)** - Groups data with an additional "overall" aggregation across all groups
- **[Normed Histogram](./normed-histogram.md)** - Creates normalized histogram from statistical data (count, mean, stdev)

### V. Psychometric & Advanced Analytics (Domain-Specific)

*High-level analytical functions that carry specific mathematical or industry intent.*

- **[Apply Cutscores](./apply-cutscores.md)** - Applies cutscore definitions to assign performance level labels based on scores
- **[Apply Discontinuation Rules](./apply-disc-rules.md)** - Applies discontinuation rules to assessment data based on domain performance
- **[Correlations](./correlations.md)** - Computes correlation coefficients, specifically point-biserial correlation
- **[Cutoffs](./cutoffs.md)** - Generates cutscore definitions based on score distributions and upper bounds
- **[WR Stats](./wr-stats.md)** - Computes Winsorized Robust statistics on a column to handle outliers

## Future Transform Categories

The categorization schema is designed to be extensible. Potential future transforms include:

### Structural
- Unpivot (wide-to-long reshaping)
- Transpose
- Partition

### Data Cleaning
- Outlier Removal
- Regex Clean

### Logic & Value Mapping
- Case-Switch
- Hash/Mask

### Aggregation & Summarization
- T-Tests
- ANOVA
- Regression

### Domain-Specific (Psychometric)
- IRT Scoring
- Item Difficulty

## Usage in DAG

Transforms are used within asset definitions to process data as it flows through the DAG. Each transform:

- Takes one or more dataframes as input (from dependencies or job config)
- Applies a specific transformation operation
- Returns a transformed dataframe
- Can be chained with other transforms through asset dependencies

See the [main DAG documentation](../index.md) for more information on how transforms fit into the overall architecture.





