# Configuring Assets and Jobs

This guide explains how to define and manage assets and jobs using the VEA Data Lakehouse Dashboard Codebook interface.

## Accessing the Codebook

The Codebook section is located in the main dashboard at `/test-ctrl/data-exporter/dashboard`. It provides a unified interface for managing both assets and jobs.

## Understanding Assets and Jobs

### Assets
Assets are the fundamental building blocks of data processing. Each asset represents:
- A data query that fetches information from the database
- A transformation that processes data from other assets
- An API call that retrieves external data

### Jobs
Jobs are collections of assets that work together to accomplish a specific data export task. Jobs define:
- Which assets to execute
- Configuration parameters required for execution
- Default values and optional settings

## Working with Assets

### Viewing Assets

1. **Navigate to Codebook**: The Codebook section appears at the top of the dashboard
2. **Use the Assets List**: In the left panel, you'll see a list of all available assets
3. **Filter Assets**: Use the "filter assets..." input box to search for specific assets by name or description
4. **View Grid**: Click "(view grid)" to see assets in a table format with sortable columns

### Inspecting an Asset

1. **Select an Asset**: Click on any asset in the list to view its details
2. **View Asset Information**: The right panel displays:
   - **Slug**: Unique identifier for the asset
   - **Caption**: Human-readable name
   - **Description**: Detailed explanation of what the asset does
   - **Structure**: Data structure type (e.g., "dataframe")
   - **Method**: Processing method (e.g., "query", "transform")
   - **Schema**: Field definitions with types, captions, and descriptions
   - **Dependency Sourcing**: How the asset depends on other assets or job configuration
   - **Transformations**: If applicable, the sequence of transformation steps

### Creating a New Asset

1. **Click "New Asset"**: Located in the Assets section header
2. **Edit in JSON Editor**: A JSON editor will open with a template asset structure
3. **Define Asset Properties**:
   ```json
   {
     "slug": "my-new-asset",
     "caption": "My New Asset",
     "description": "Description of what this asset does",
     "structure": "dataframe",
     "method": "query",
     "methodConfig": {
       // Configuration specific to the method type
     }
   }
   ```
4. **Validate JSON**: The editor will highlight any JSON syntax errors
5. **Save Options**:
   - **Create Preview**: Saves as a preview version for testing
   - **Save Live**: Saves as the live version (use with caution)
   - **Cancel**: Discards changes

### Editing an Existing Asset

1. **Select the Asset**: Click on the asset you want to edit
2. **Click "Raw Edit"**: Located in the asset detail toolbar
3. **Modify JSON**: Edit the asset definition in the JSON editor
4. **Review Changes**: The interface highlights sections that differ from the current version
5. **Save**: Choose "Create Preview" to test changes or "Save Live" to apply immediately

### Preview vs Live Versions

The dashboard supports a preview workflow for safe testing:

- **Preview Versions**: Test changes without affecting production
- **Live Versions**: The current production version
- **Version Comparison**: The dashboard shows differences between preview and live versions
- **Publishing**: Use "Publish Preview" to promote a preview version to live

## Working with Jobs

### Viewing Jobs

1. **Use the Jobs List**: In the left panel, below the Assets section
2. **Filter Jobs**: Use the "filter jobs..." input box to search
3. **View Grid**: Click "(view grid)" to see jobs in a table format

### Inspecting a Job

1. **Select a Job**: Click on any job in the list
2. **View Job Information**: The right panel displays:
   - **Slug**: Unique identifier
   - **Description**: What the job accomplishes
   - **Scope**: Execution scope (e.g., "schools", "test-centres")
   - **Assets**: List of assets included in the job
   - **Required Parameters**: Configuration parameters that must be provided
   - **Optional Parameters**: Additional configuration options
   - **Default Config**: Default values for configuration parameters

### Creating a New Job

1. **Click "New Job"**: Located in the Jobs section header
2. **Edit in JSON Editor**: A JSON editor will open with a template job structure
3. **Define Job Properties**:
   ```json
   {
     "slug": "my-new-job",
     "description": "Description of what this job does",
     "scope": "schools",
     "assets": [
       "asset-slug-1",
       "asset-slug-2"
     ],
     "pipeline_config_req": {
       "test_window_ids": "number[]"
     },
     "pipeline_config_opt": {
       "schl_dist_group_ids": "number[]"
     },
     "pipeline_config": {
       "test_window_ids": [],
       "schl_dist_group_ids": []
     }
   }
   ```
4. **Validate JSON**: Check for syntax errors
5. **Save**: Choose "Create Preview" or "Save Live"

### Editing an Existing Job

1. **Select the Job**: Click on the job you want to edit
2. **Click "Raw Edit"**: Located in the job detail toolbar
3. **Modify JSON**: Edit the job definition
4. **Review Changes**: Check highlighted differences
5. **Save**: Use "Create Preview" for testing or "Save Live" to apply

## Using Preview Mode

The "Use Preview Jobs/Assets" toggle at the top of the Codebook panel allows you to:

- **Toggle Preview Mode**: Switch between preview and live versions
- **Test Changes**: Work with preview versions without affecting production
- **Compare Versions**: See differences between preview and live

**Note**: When creating export jobs, you can also choose to use preview assets and jobs in the export configuration.

## Asset Dependencies

When viewing an asset, you can see:

- **Upstream Assets**: Assets that this asset depends on
- **Downstream Assets**: Assets that depend on this asset
- **Dependency Sourcing**: How dependencies are configured:
  - **Job Config**: Values from job configuration parameters
  - **Asset Columns**: Specific columns from other assets
  - **Dataframes**: Entire dataframes from other assets

## Best Practices

1. **Always Use Preview First**: Create preview versions to test changes before saving live
2. **Validate JSON**: Ensure your JSON is valid before saving
3. **Check Dependencies**: Verify that referenced assets exist and are correctly named
4. **Document Changes**: Use clear descriptions and captions
5. **Test Thoroughly**: Use preview mode to test job configurations before promoting to live

## Common Asset Types

### Query Assets
Assets that fetch data directly from the database:
```json
{
  "method": "query",
  "methodConfig": {
    "querySlug": "query-name",
    "params": {}
  }
}
```

### Transform Assets
Assets that process data from other assets:
```json
{
  "method": "transform",
  "methodConfig": {
    "sequence": [
      {
        "kind": "group-by",
        "config": { /* transform config */ }
      }
    ]
  }
}
```

## Troubleshooting

### JSON Validation Errors
- Check for missing commas, brackets, or quotes
- Verify all required fields are present
- Use the "Show Error" button for detailed error messages

### Asset Not Found
- Verify the asset slug is spelled correctly
- Check that you're using the correct version (preview vs live)
- Ensure the asset exists in the system

### Dependency Errors
- Verify all referenced assets exist
- Check that dependency configurations match the asset structure
- Review the dependency sourcing configuration

