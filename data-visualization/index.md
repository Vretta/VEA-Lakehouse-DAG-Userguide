# Data Visualization

## Overview

The VEA Data Lakehouse DAG system supports data visualization through **Vega**, a declarative language for creating interactive visualizations. Graphs can be embedded directly in job definitions as part of the `alternativePackage` configuration, allowing you to create rich, interactive visualizations that are automatically generated alongside your data exports.

## Vega Integration

Vega is a visualization grammar that provides a declarative way to create interactive graphics. The system uses **Vega v5** schema for graph definitions. When graphs are included in a job definition, they are rendered automatically when the job results are viewed or exported.

## Graph Configuration in Job Definitions

Graphs are defined within the `alternativePackage` section of a job definition. The `alternativePackage` can contain both:
- **Assets**: Data tables with custom captions
- **Graphs**: Vega-based visualizations

### Basic Structure

```typescript
{
  "alternativePackage": {
    "assets": [
      {
        "slug": "asset_slug",
        "caption": "Display Caption"
      }
    ],
    "graphs": [
      {
        "type": "vega",
        "config": {
          "mappings": [...],
          "structure": {...}
        },
        "caption": "Graph Display Caption"
      }
    ]
  }
}
```

## Graph Configuration Components

### 1. Graph Type

Currently, only `"vega"` type is supported:

```json
{
  "type": "vega"
}
```

### 2. Mappings

Mappings connect graph data sources to job assets. Each mapping specifies:
- **path**: The JSON path within the Vega specification where asset data should be injected
- **asset**: The asset slug whose data will be used

```json
{
  "mappings": [
    {
      "path": "data.0.values",
      "asset": "trfm_attempt_logins_by_date"
    }
  ]
}
```

The `path` typically points to the `values` array of a data source in the Vega specification. The system automatically populates this array with rows from the specified asset.

### 3. Structure

The `structure` field contains the complete Vega v5 specification. This defines:
- **Data sources**: Where data comes from (populated via mappings)
- **Scales**: Axes, colors, sizes
- **Marks**: Visual elements (lines, bars, symbols, etc.)
- **Axes**: Axis configurations
- **Legends**: Legend definitions
- **Layout**: Faceting and grouping

### 4. Caption

A human-readable caption displayed with the graph:

```json
{
  "caption": "[M1] Logins over Time"
}
```

## Example: Complete Job Definition with Graphs

The following example shows a job definition that includes multiple Vega graphs for visualizing assessment data:

```json
{
  "slug": "reports-school-board-simple",
  "assets": [
    "trfm_attempt_logins_by_date",
    "trfm_attempt_submissions_by_date",
    "trfm_s_summary_participation_by_ta",
    "trfm_g_summary_participation_by_ta"
  ],
  "description": "Generates summary reports for Vault school boards, including basic item summaries, total scores, and participation summaries.",
  "pipeline_config": {
    "include_sample_schools": true,
    "include_sample_assessments": true,
    "restrict_to_submitted_attempts": true
  },
  "alternativePackage": {
    "assets": [
      {
        "slug": "trfm_attempt_logins_by_date",
        "caption": "[M1] Logins over Time"
      }
    ],
    "graphs": [
      {
        "type": "vega",
        "config": {
          "mappings": [
            {
              "path": "data.0.values",
              "asset": "trfm_attempt_logins_by_date"
            }
          ],
          "structure": {
            "axes": [
              {
                "grid": true,
                "scale": "xscale",
                "title": "Date",
                "format": "%b %d",
                "orient": "bottom"
              },
              {
                "grid": true,
                "scale": "yscale",
                "title": "Total Submissions",
                "orient": "left"
              }
            ],
            "data": [
              {
                "name": "source_0",
                "format": {
                  "type": "json",
                  "parse": {
                    "started_on_date": "date"
                  }
                },
                "values": [],
                "transform": [
                  {
                    "sort": {
                      "field": ["assessment_code", "started_on_date"]
                    },
                    "type": "collect"
                  },
                  {
                    "as": ["cumulative_submissions"],
                    "ops": ["sum"],
                    "type": "window",
                    "frame": [null, 0],
                    "fields": ["count"],
                    "groupby": ["assessment_code"]
                  }
                ]
              }
            ],
            "marks": [
              {
                "from": {
                  "facet": {
                    "data": "source_0",
                    "name": "faceted_data",
                    "groupby": "assessment_code"
                  }
                },
                "type": "group",
                "marks": [
                  {
                    "from": {
                      "data": "faceted_data"
                    },
                    "type": "line",
                    "encode": {
                      "enter": {
                        "x": {
                          "field": "started_on_date",
                          "scale": "xscale"
                        },
                        "y": {
                          "field": "cumulative_submissions",
                          "scale": "yscale"
                        },
                        "stroke": {
                          "field": "assessment_code",
                          "scale": "color"
                        },
                        "strokeWidth": {
                          "value": 2
                        }
                      }
                    }
                  },
                  {
                    "from": {
                      "data": "faceted_data"
                    },
                    "type": "symbol",
                    "encode": {
                      "enter": {
                        "x": {
                          "field": "started_on_date",
                          "scale": "xscale"
                        },
                        "y": {
                          "field": "cumulative_submissions",
                          "scale": "yscale"
                        },
                        "fill": {
                          "field": "assessment_code",
                          "scale": "color"
                        },
                        "size": {
                          "value": 30
                        },
                        "tooltip": {
                          "signal": "{'Assessment': datum.assessment_code, 'Date': timeFormat(datum.started_on_date, '%b %d, %Y'), 'Total Submissions': datum.cumulative_submissions}"
                        }
                      }
                    }
                  }
                ]
              }
            ],
            "width": 700,
            "height": 300,
            "scales": [
              {
                "name": "xscale",
                "type": "time",
                "range": "width",
                "domain": {
                  "data": "source_0",
                  "field": "started_on_date"
                }
              },
              {
                "name": "yscale",
                "nice": true,
                "type": "linear",
                "zero": true,
                "range": "height",
                "domain": {
                  "data": "source_0",
                  "field": "cumulative_submissions"
                }
              },
              {
                "name": "color",
                "type": "ordinal",
                "range": "category",
                "domain": {
                  "data": "source_0",
                  "field": "assessment_code"
                }
              }
            ],
            "$schema": "https://vega.github.io/schema/vega/v5.json",
            "legends": [
              {
                "title": "Assessment",
                "stroke": "color",
                "symbolType": "circle"
              }
            ],
            "padding": 5,
            "autosize": "pad",
            "description": "Cumulative Logins over Time per Assessment"
          }
        },
        "caption": "[M1] Logins over Time"
      }
    ]
  }
}
```

## Common Graph Patterns

### Time Series Line Chart

Visualize data over time with cumulative calculations:

- **Use case**: Tracking logins, submissions, or other time-based metrics
- **Key features**: Time scale on x-axis, cumulative window transforms, faceting by category
- **Example**: Logins over time per assessment

### Stacked Bar Chart

Show composition breakdown across categories:

- **Use case**: Participation status by school, grade, or other grouping
- **Key features**: Stack transform, categorical color scales, grouped facets
- **Example**: Participation breakdown by school

### Donut/Pie Chart

Display proportional breakdowns:

- **Use case**: Participation levels, status distributions
- **Key features**: Arc marks, normalized stack transforms, faceted layouts
- **Example**: Participation by grade with multiple sub-windows

### Bar Chart with Color Gradient

Show values with color intensity:

- **Use case**: Performance metrics, percentages
- **Key features**: Linear color scales, gradient legends
- **Example**: Participation percentage by school

## Data Transformations in Vega

Vega supports powerful data transformations that can be applied before visualization:

### Window Transforms

Calculate running totals, rankings, or other windowed aggregations:

```json
{
  "type": "window",
  "ops": ["sum"],
  "fields": ["count"],
  "frame": [null, 0],
  "groupby": ["assessment_code"],
  "as": ["cumulative_submissions"]
}
```

### Stack Transforms

Create stacked visualizations:

```json
{
  "type": "stack",
  "field": "count",
  "groupby": ["s_name"],
  "as": ["y0", "y1"]
}
```

### Formula Transforms

Calculate derived values:

```json
{
  "type": "formula",
  "as": "participation_type",
  "expr": "datum.participation_slug == 'n_partic_none' ? 'No Participation' : 'Full Participation'"
}
```

### Facet Transforms

Group data for small multiples:

```json
{
  "type": "facet",
  "groupby": ["assessment_code"],
  "name": "faceted_data"
}
```

## Best Practices

1. **Data Preparation**: Ensure your asset data is in the format expected by the Vega specification
   - Date fields should be parseable as dates
   - Numeric fields should be actual numbers, not strings
   - Categorical fields should have consistent values

2. **Mapping Paths**: Always use empty arrays `[]` for `values` in your Vega data sources - the system will populate them automatically

3. **Schema Version**: Always include `"$schema": "https://vega.github.io/schema/vega/v5.json"` in your structure

4. **Descriptive Captions**: Use clear, descriptive captions that match the graph content

5. **Tooltips**: Include tooltip signals for interactive exploration:
   ```json
   "tooltip": {
     "signal": "{'Label': datum.field_name, 'Value': format(datum.value, ',')}"
   }
   ```

6. **Responsive Sizing**: Use `"autosize": "pad"` for automatic padding, or specify fixed `width` and `height`

7. **Color Scales**: Use meaningful color scales:
   - `"range": "category"` for categorical data
   - Custom arrays like `["#d62728", "#2ca02c"]` for sequential data
   - Gradient scales for continuous data

8. **Testing**: Test your graphs with sample data to ensure transforms and scales work correctly

## Resources

- [Vega Documentation](https://vega.github.io/vega/docs/)
- [Vega v5 Schema Reference](https://vega.github.io/schema/vega/v5.json)
- [Vega Examples Gallery](https://vega.github.io/vega/examples/)

## See Also

- [Jobs Model](./models/jobs.md) - Complete job definition reference
- [Assets Model](./models/assets.md) - Asset structure and configuration

