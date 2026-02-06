# Data Lakehouse DAG Architecture

## Overview

The Data Lakehouse DAG (Directed Acyclic Graph) system is a comprehensive data processing and export framework that enables the creation, execution, and management of complex data transformation pipelines in the context of the Vretta e-Assessment platform. The system is built on a DAG-based architecture where data flows through a series of interconnected assets (nodes) that perform various operations such as queries, transformations, and API calls.

## Core Concepts

### DAG (Directed Acyclic Graph)
A DAG is a graph structure where:
- **Nodes (Assets)**: Represent data processing operations
- **Edges (Dependencies)**: Represent data flow between assets
- **Acyclic**: No circular dependencies allowed
- **Execution Order**: Determined by topological sorting of dependencies

### Assets
Assets are the fundamental building blocks of the DAG. Each asset represents a data processing step that:
- Produces a data output (dataframe, file, etc.)
- May depend on other assets' outputs
- Can be configured with various parameters
- Has a specific method (query, transform, API call, etc.)

### Jobs
Jobs are collections of assets that work together to accomplish a specific data export or processing task. Jobs define:
- Which assets to execute
- Configuration parameters (pipeline_config)
- Required and optional parameters
- Scope (schools, test-centres, etc.)

## Architecture Components

### 1. Data Models
- [Assets Model](./models/assets.md): Defines asset structure, dependencies, and configuration
- [Jobs Model](./models/jobs.md): Defines job structure and configuration
- [Type Definitions](./models/types.md): Core TypeScript type definitions

### 2. Queries

Data export queries fetch data from the database and produce structured outputs. See the [Queries Index](./queries/index.md) for a complete categorized list of all available queries with detailed schema definitions.

### 3. Transform Functions
Transform functions are pure data transformation operations. See the [Transform Functions Index](./transforms/index.md) for a complete categorized list of all available transforms.

### 4. Data Visualization
The system supports interactive data visualization using Vega. Graphs can be embedded directly in job definitions to create rich visualizations alongside data exports. See [Data Visualization](./data-visualization/index.md) for details on configuring Vega graphs in job definitions.

### 5. Web Interface

The dashboard provides a web-based interface for managing assets, jobs, and exports. See the [Web UI Guide](./web-ui/index.md) for tutorials on configuring assets and jobs, and running export jobs.

## Data Flow

1. **Job Creation**: Client creates an export job with configuration
2. **Asset Resolution**: System resolves asset dependencies and builds execution sequence
3. **Execution**: Assets are executed in dependency order:
   - Query assets fetch data from database
   - Transform assets process data
   - API method assets call external APIs
4. **Storage**: Results are stored in S3 as JSON files
5. **Packaging**: Final assets are packaged into downloadable files
6. **Completion**: Job status updated and download URLs provided

## Key Features

### Partitioning
Assets can be partitioned by:
- **Numeric partitioning**: Split data by numeric ranges (e.g., by ID ranges)
- **Categorical partitioning**: Split data by categorical values (e.g., by school_id)

### Dependency Sourcing
Assets can source dependencies from:
- **Job Config**: Pipeline configuration parameters
- **Asset Columns**: Extract values from other assets' columns
- **Dataframes**: Use entire dataframes from other assets

### Execution Modes
- **Background Jobs**: Asynchronous execution
- **Foreground Jobs**: Synchronous execution (for small jobs)
- **Dev Mode**: Development/testing mode with different storage

## Quick Start

1. **Define Assets**: Create asset definitions in `data-export/model/assets.ts`
2. **Define Jobs**: Create job definitions in `data-export/model/jobs.ts`
3. **Create Transform**: Implement transform function in `data-export-queries/transforms/`
4. **Register Transform**: Add transform to service in `data-export-queries/services/transforms.ts`
5. **Test**: Create test export job via API

## Type Definitions Reference

See [Type Definitions](./models/types.md) for complete TypeScript interfaces and types used throughout the system.

