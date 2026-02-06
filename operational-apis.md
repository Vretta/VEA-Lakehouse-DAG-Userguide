# Operational APIs and Execution Model

## Overview

The Data Lakehouse DAG system executes export jobs through operational APIs that run on shared infrastructure. This document describes the execution model, benefits, risks, and mitigation strategies.

## Execution Model

### API-Based Execution

Export jobs are executed through the main application API service (`/public/data-exporter/data-export`). When a job is created:

1. **Job Creation**: Client creates export job via `POST /public/data-exporter/data-export`
2. **Immediate Execution**: Job executes synchronously or asynchronously based on `isMainExport` flag
3. **In-Process Execution**: All asset processing occurs within the API server process
4. **Memory-Based Processing**: Dataframes are loaded into memory for transformation operations
5. **S3 Storage**: Results are stored in S3 as JSON files

### Execution Flow

```
Client Request
    ↓
API Service (Node.js Process)
    ↓
Job Execution (In-Process)
    ├── Asset 1: Query → Memory → Transform → S3
    ├── Asset 2: Query → Memory → Transform → S3
    └── Asset 3: Transform (Memory) → S3
    ↓
Job Completion
    ↓
Response to Client
```

## Benefits of Shared Capacity

### Resource Efficiency

- **Shared Infrastructure**: Multiple export jobs can run on the same API server cluster
- **Dynamic Allocation**: Resources are allocated on-demand based on job requirements
- **Cost Optimization**: No need for dedicated infrastructure per job type
- **Simplified Deployment**: Single deployment model for all export operations

### Operational Simplicity

- **Unified Monitoring**: All jobs visible through same monitoring infrastructure
- **Consistent Logging**: Centralized logging and debugging
- **Easy Scaling**: Scale API servers horizontally to handle load
- **Integrated Authentication**: Uses existing API authentication and authorization

## Memory Overrun Risks

### Risk Factors

The in-process execution model introduces memory risks:

1. **Large Datasets**: Export jobs can process millions of records
2. **Multiple Assets**: Jobs may have 50+ assets, each loading data into memory
3. **Concurrent Jobs**: Multiple jobs running simultaneously on same server
4. **Transform Operations**: Complex transforms (joins, group-bys) create intermediate dataframes
5. **No Memory Limits**: Node.js processes can consume all available system memory

### Potential Impact

- **Process Crashes**: Out-of-memory errors can crash the API server
- **Service Degradation**: High memory usage affects other API operations
- **Job Failures**: Jobs may fail mid-execution due to memory exhaustion
- **Cascading Failures**: One large job can affect all jobs on the same server

## Risk Mitigation: Asset Partitioning

### Partitioning Strategy

The primary mitigation strategy is **asset partitioning**, which breaks large datasets into smaller, manageable chunks:

#### Numeric Partitioning

Partition data by numeric ranges:

```typescript
{
  partitionOut: {
    key: "attempt_id",
    field: "attempt_id",
    partSize: 10000  // Partition every 10,000 IDs
  }
}
```

**Benefits:**
- Processes data in smaller chunks
- Reduces peak memory usage
- Enables parallel processing of partitions
- Allows processing of datasets larger than available memory

#### Categorical Partitioning

Partition data by categorical values:

```typescript
{
  partitionOut: {
    key: "school_id",
    field: "school_id",
    categorical: true  // One partition per unique school_id
  }
}
```

**Benefits:**
- Natural data boundaries (e.g., by school, district)
- Enables independent processing of partitions
- Supports downstream partitioning strategies
- Reduces memory footprint per partition

### Partitioning Best Practices

1. **Partition Early**: Partition large datasets as early as possible in the pipeline
2. **Appropriate Size**: Choose partition size based on available memory (typically 5,000-50,000 records)
3. **Partition By**: Use `partitionBy` to process already-partitioned data without loading all partitions
4. **Concat When Needed**: Use `concatPartitionedAsset` only when final output requires single file
5. **Avoid Unnecessary Concatenation**: Keep data partitioned as long as possible

### Example: Partitioned Asset Flow

```
load_attempts (partitioned by attempt_id, 10k chunks)
    ↓
trfm_attempt_scores (partitionBy: attempt_id, processes each partition)
    ↓
trfm_aggregate_by_school (repartitionOut: school_id, categorical)
    ↓
concatPartitionedAsset (only if final output needs single file)
```

## Additional Mitigation Strategies

### 1. Query Optimization

- **Chunked Queries**: Use `query-chunked` method for large datasets
- **Selective Columns**: Only select required columns in queries
- **Filtering**: Apply filters at database level, not in memory
- **Indexing**: Ensure database indexes support query patterns

### 2. Memory Management

- **Cache Strategy**: Use `storeCache` selectively (only for assets needed by multiple downstream assets)
- **Early S3 Storage**: Store intermediate results in S3 rather than keeping in memory
- **Garbage Collection**: Allow Node.js garbage collection between asset processing
- **Memory Monitoring**: Monitor memory usage and set alerts

### 3. Job Configuration

- **Background Jobs**: Use background execution for large jobs to avoid blocking API
- **Job Size Limits**: Consider implementing maximum dataset size limits
- **Concurrent Job Limits**: Limit number of concurrent export jobs per server
- **Priority Queuing**: Implement job priority to ensure critical jobs complete

### 4. Asset Design

- **Incremental Processing**: Design assets to process data incrementally
- **Streaming Where Possible**: Use streaming for very large datasets (future enhancement)
- **Avoid Large Joins**: Break large joins into smaller operations
- **Transform Efficiency**: Use efficient transform algorithms (e.g., sort-merge join)

## Secondary Cluster Management

### Current Architecture

Currently, all export jobs execute on the primary API server cluster. This provides simplicity but limits scalability and isolation.

### Benefits of Secondary Clusters

- **Isolation**: Export jobs don't impact main API performance
- **Specialized Resources**: Optimize cluster for data processing workloads
- **Independent Scaling**: Scale export capacity based on export job demand
- **Better Resource Management**: Dedicated memory and CPU for export operations
- **Improved Reliability**: Failures in export jobs don't cascade to API operations

### Migration Considerations

When implementing secondary clusters:

1. **Job Routing**: Route export jobs to appropriate cluster based on size/complexity
2. **State Management**: Ensure job state is accessible from both clusters
3. **S3 Access**: Both clusters need access to same S3 bucket
4. **Monitoring**: Unified monitoring across both clusters
5. **Gradual Migration**: Migrate jobs incrementally to validate approach

## Monitoring and Observability

### Key Metrics

Monitor the following to detect memory issues:

- **Memory Usage**: Per-process and per-job memory consumption
- **Job Duration**: Long-running jobs may indicate memory pressure
- **Job Failure Rate**: Memory-related failures
- **Concurrent Jobs**: Number of simultaneous export jobs
- **Asset Processing Time**: Slow assets may indicate memory issues
- **S3 Storage Growth**: Large intermediate storage may indicate memory pressure

### Alerting

Set up alerts for:

- Memory usage exceeding thresholds (e.g., >80% of available)
- Job failures with out-of-memory errors
- Jobs taking longer than expected
- Multiple concurrent large jobs

## Best Practices Summary

1. **Always Partition Large Assets**: Any asset processing >100k records should be partitioned
2. **Use Chunked Queries**: For queries returning large result sets
3. **Store to S3 Early**: Don't keep large datasets in memory cache unnecessarily
4. **Monitor Memory**: Track memory usage patterns and adjust partitioning accordingly
5. **Design for Scale**: Assume jobs will grow in size over time
6. **Test with Production-Sized Data**: Validate memory usage with realistic data volumes
7. **Document Memory Requirements**: Note expected memory usage for each job type

## Related Documentation

- [Storage Operations](./storage/index.md) - Partitioning implementation details
- [Asset Model](./models/assets.md) - Asset partitioning configuration
- [Data Export Service](./services/data-export.md) - Job execution service
- [Secondary Cluster Management](../../implementation-proposals/2026/01/multiplex/secondary-cluster-management.md) - Future architecture proposal

