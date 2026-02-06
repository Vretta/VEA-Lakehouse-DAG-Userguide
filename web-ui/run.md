# Running Export Jobs

This guide explains how to create and execute export jobs using the VEA Data Lakehouse Dashboard.

## Accessing Export Jobs

The Export Jobs section is located in the main dashboard at `/test-ctrl/data-exporter/dashboard`, below the Codebook section.

## Understanding Export Jobs

Export jobs execute a defined job (collection of assets) with specific configuration parameters to produce downloadable data packages. Each export job:

- Executes all assets in the selected job
- Processes data according to the job configuration
- Generates downloadable data files
- Tracks execution status and completion

## Creating a New Export Job

### Step 1: Open the Export Configuration

1. **Click "New Export Job"**: Located at the top of the Export Jobs section
2. **Configuration Panel Opens**: A form will appear below the button

### Step 2: Select a Job

1. **Choose Job**: Use the "Job" dropdown to select from available jobs
2. **Job List**: The dropdown shows all jobs defined in the Codebook
3. **Job Details**: After selection, job configuration options will appear

### Step 3: Configure Job Parameters

Once a job is selected, the interface displays configuration options based on the job's required and optional parameters:

#### Required Parameters
These must be provided for the job to run:

- **test_window_ids** (number[]): Array of test window IDs
  - Click "init array" if the field is empty
  - Use "Add Number" to add test window IDs
  - Use "Remove" to delete entries

#### Optional Parameters
These can be configured but have default values:

- **String Parameters**: Text input fields
- **Number Parameters**: Numeric input fields
- **Boolean Parameters**: Checkboxes
- **Array Parameters**: Similar to required arrays, with "init array" and add/remove controls

#### Additional Options

- **Purpose (optional)**: Add a description for this export job
- **Use Preview Assets and Job**: Checkbox to use preview versions instead of live versions

### Step 4: Load Job Configuration (Optional)

1. **Click "Load JSON"**: Loads the job's default configuration
2. **Review Defaults**: See the default values for all parameters
3. **Modify as Needed**: Adjust parameters in the configuration form

### Step 5: Run the Job

1. **Review Configuration**: Verify all required parameters are set
2. **Click "Run Job"**: Starts the export job execution
3. **Job Appears in List**: The new job will appear in the export jobs list

### Step 6: Cancel (if needed)

- **Click "Cancel"**: Closes the configuration panel without creating a job
- **Click "Cancel Export"**: Cancels any in-progress export (if available)

## Monitoring Export Jobs

### Viewing the Export List

The main export jobs table displays:

- **Export ID**: Unique identifier for the export
- **Status**: Current status (e.g., "COMPLETE", "RUNNING", "FAILED")
- **Job Name**: The job that was executed
- **Checkbox**: Select an export to view details

### Viewing Export Details

1. **Select an Export**: Click the checkbox next to an export in the list
2. **Details Panel**: The right panel shows:
   - **Export ID**: Unique identifier
   - **Associated Administration Window ID**: Test window ID
   - **Description**: Purpose/description of the export
   - **Job Configuration**: Raw JSON configuration used
   - **Export Started On**: Timestamp when execution began
   - **Export Completed On**: Timestamp when execution finished
   - **Download**: Link to download the export package

### Refreshing Job Status

1. **Click "Refresh Job Status"**: Updates the status of the selected export
2. **Auto-Refresh**: The interface may auto-refresh periodically for running jobs

### Filtering Exports

- **Include Background Jobs**: Toggle to show/hide background jobs
- **Refresh Export List**: Click to reload the list manually

## Downloading Export Results

### Download Complete Package

1. **Wait for Completion**: Ensure the export status is "COMPLETE"
2. **Find Download Link**: In the export details panel, locate the "Download" section
3. **Click "Export Zip Package"**: Downloads a ZIP file containing all export assets

### Viewing Individual Assets

1. **Select Completed Export**: Choose a completed export from the list
2. **Scroll to "DOWNLOADABLE DATA ASSETS"**: Below the export details
3. **Expand Asset Categories**: Click on category headers to expand:
   - **KEY OUTPUTS**: Primary data assets
   - Other categories as defined by the job
4. **View Asset Details**: Each asset shows:
   - **View Button**: Click to inspect the asset data
   - **Slug**: Asset identifier
   - **Caption**: Human-readable name
   - **Description**: What the asset contains
   - **Record Count**: Number of records
   - **Progress Bar**: Completion status

### Inspecting Asset Data

1. **Click "View"**: Next to any asset in the downloadable assets list
2. **Asset Grid Opens**: A data grid appears below showing the asset contents
3. **Browse Data**: Scroll through rows and columns
4. **Export as CSV**: Click "Export Asset CSV" to download the asset as a CSV file

## Understanding Export Status

### Status Indicators

- **COMPLETE**: Export finished successfully
- **RUNNING**: Export is currently executing
- **FAILED**: Export encountered an error
- **PENDING**: Export is queued for execution

### Progress Tracking

- **Progress Bars**: Individual assets show progress bars
- **Color Coding**:
  - **Green**: Asset completed successfully
  - **Yellow/Orange**: Asset in progress
- **Checkmarks**: Completed assets show a checkmark icon

## Export Job Configuration Examples

### Basic School Board Report

```
Job: reports-school-board
Required Parameters:
  - test_window_ids: [123, 456]
Optional Parameters:
  - schl_dist_group_ids: [1, 2, 3]
Purpose: "Q1 2024 School Board Report"
```

### Simple Report with Preview Assets

```
Job: reports-school-board-simple
Required Parameters:
  - test_window_ids: [789]
Use Preview Assets and Job: ✓ (checked)
Purpose: "Testing new asset definitions"
```

## Best Practices

1. **Use Descriptive Purposes**: Always fill in the "Purpose" field to track why exports were created
2. **Test with Preview**: Use preview assets and jobs when testing new configurations
3. **Monitor Progress**: Check export status regularly for long-running jobs
4. **Download Promptly**: Download completed exports while they're available
5. **Verify Data**: Use the "View" feature to inspect asset contents before downloading
6. **Document Configuration**: Note any custom parameter values for future reference

## Troubleshooting

### Job Won't Start

- **Check Required Parameters**: Ensure all required parameters are provided
- **Verify Parameter Types**: Numbers should be numbers, arrays should be arrays
- **Check Job Status**: Ensure the job definition is active (not disabled)

### Export Stuck in "RUNNING"

- **Wait**: Some exports take time, especially for large datasets
- **Refresh Status**: Click "Refresh Job Status" to get the latest status
- **Check Background Jobs**: Enable "Include Background Jobs" if the export is a background job

### Download Link Not Available

- **Wait for Completion**: Ensure the export status is "COMPLETE"
- **Refresh**: Click "Refresh Job Status" to update the download link
- **Check Asset Contents**: Some exports may show individual assets instead of a package download

### Asset Data Not Loading

- **Check Export Status**: Ensure the export is complete
- **Refresh**: Use "Refresh Job Status" to reload asset contents
- **Verify Asset Completion**: Check that individual assets show as complete (green progress bar)

### Configuration Errors

- **Validate JSON**: If loading JSON, ensure it's valid
- **Check Parameter Types**: Verify parameter types match job requirements
- **Review Job Definition**: Check the job's required and optional parameters in the Codebook

## Advanced Features

### Background Jobs

- **Toggle "Include Background Jobs"**: Shows background export jobs in the list
- **Background Execution**: Some jobs run asynchronously in the background
- **Status Updates**: Background jobs may take longer to show status updates

### Job Configuration JSON

- **Show Raw**: Click "Show Raw" to view/edit the full JSON configuration
- **Manual Editing**: Advanced users can edit the JSON directly
- **Validation**: The system validates configuration before execution

