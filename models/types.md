# Type Definitions

This document contains all TypeScript type definitions used in the Data Lakehouse DAG system.

## Source File
`api--abed/src/services/public/data-exporter/data-export-queries/types/type.ts`

## Core Types

### Job Types

```typescript
export enum JobType {
    JOB = 'job',
    ASSET = 'asset',
    COMPOUND = 'compound',
}
```

### Export Job

```typescript
export interface IExportJob {
  id: number,
  test_window_id?: number,
  created_on: any,
  path_hash: string,
  is_bg: number
  created_by_uid: number,
  dag_job_name: string,
  description: string,
}
```

### DAG Job Config

```typescript
export interface IDagJobConfig {
    jobType: JobType,
    jobName?: string,
    notes: string,
    isBg?: boolean,
    testWindowId?: number,
    canonicalTagContext?: ICanonicalTagContext
    config: {[key: string]: any} // pipeline_config options
}
```

### Canonical Tag Context

```typescript
export interface ICanonicalTagContext {
    slug?: string, // if this is undefined, no tag will be saved/retrieved
    test_window_id?: number,
    twtar_id?: number,
    schl_dist_group_id?: number,
    schl_group_id?: number,
}
```

### Active Job

```typescript
export interface IActiveJob {
    exportId: number,
    status: string,
    storagePath: string,
    jobDagState: IDagJobState,
}
```

### DAG Job State

```typescript
export interface IDagJobState {
    assetsSlugs: AssetSlug[],
    assetDependencies: {
        [assetSlug: AssetSlug]: AssetSlug[]
    }
    assetCompletions: {
        [assetSlug: AssetSlug]: {
            isReady: boolean,
            isStarted: boolean,
            isError: boolean,
            errorDetails: any[],
            startedOn: number,
            completedOn: number,
            delayBeforeStartMs: number,
            durationMs: number,
            numberOfParts: number,
            benchmarkTime: number,
        }
    }
}
```

## Asset Types

### Export Asset

```typescript
export interface IExportAsset {
    slug: string,
    description: string,
    structure: IExportAssetStructure | string,
    structureConfig: IExportAssetStructureConfig,
    method: IExportAssetMethod | string,
    methodConfig: IExportAssetMethodConfig,
    dependencySourcings: ISourcing[],
}
```

### Export Asset Structure

```typescript
enum IExportAssetStructure {
    DATAFRAME = 'dataframe',
    DATAFRAME_CHUNKED = 'dataframe-chunked',
    ZIP = 'zip',
    EXCEL = 'excel',
}
```

### Export Asset Method

```typescript
enum IExportAssetMethod {
    QUERY = 'query',
    QUERY_CHUNKED = 'query-chunked',
    TRANSFORM = 'transform',
    API = 'api',
    API_CHUNKED = 'api-chunked',
    PACKAGE = 'package',
    UNPACK = 'unpack',
    UPLOAD_AND_WAIT = 'upload-and-wait',
}
```

### Method Configs

```typescript
interface IExportAssetMethodConfigQuery extends IExportAssetMethodConfig {
    querySlug: string,
}

interface IExportAssetMethodConfigQueryChunked extends IExportAssetMethodConfigQuery {
    chunkSize: number,
}

interface IExportAssetMethodConfigTransform extends IExportAssetMethodConfig {
    transformSlug: string,
}

interface IExportAssetMethodConfigApi extends IExportAssetMethodConfig {
    method: string,
    path: string,
    params: any,
    data: any,
}

interface IExportAssetMethodConfigApiChunked extends IExportAssetMethodConfigApi {
    chunkSize: number,
}
```

## Dataframe Types

### Value Types

```typescript
export type Value = number | string | boolean
export type IRecord = {[key: string]: Value}
```

### Record List

```typescript
interface IRecordList {
    schema: string[],
    data: IRecord[],
}
```

### Columnar Frame

```typescript
interface IColumnarFrame {
    schema: string[]
    data: IDataframeColumn[]
}
```

### Dataframe Column

```typescript
interface IDataframeColumn {
    name: string,
    type: DataframeColType,
    isNullable: boolean,
}
```

### Dataframe Column Types

```typescript
enum DataframeColType {
    NUM_BOOLEAN = 'NUM_BOOLEAN',
    NUM_INT = 'NUM_INT',
    NUM_INT_UNSIGNED = 'NUM_INT_UNSIGNED',
    NUM_FLOAT = 'NUM_FLOAT',
    NUM_TIMESTAMP_UNIX = 'NUM_TIMESTAMP_UNIX',
    NUM_TIME_MS = 'NUM_TIME_MS',
    TXT_SLUG = 'TXT_SLUG',
    TXT_SMALL = 'TXT_SMALL',
    TXT_LONG = 'TXT_LONG',
    TXT_JSON = 'TXT_JSON',
    TXT_TIMESTAMP_ISO = 'TXT_JSON',
}
```

## Query and Transform Definitions

### Export Query Def

```typescript
export interface IExportQueryDef extends IExportAssetMaterializaerDef {
    queryGen: (config: any) => string,
}
```

### Export Transform Def

```typescript
export interface IExportTransformDef extends IExportAssetMaterializaerDef {
    transform: (config: any) => string,
}
```

### Export Api Call Def

```typescript
export interface IExportApiCallDef extends IExportAssetMaterializaerDef {
    req: (app: Application, config: any) => PromiseLike<any[]>,
}
```

### Export Asset Materializer Def

```typescript
export interface IExportAssetMaterializaerDef {
    requiredInputs: string[],
    optionalInputs?: string[],
}
```

## Sourcing Types

### Sourcing

```typescript
interface ISourcing {
    param: string,
    type: SourcingType,
    config: ISourcingConfig,
}
```

### Sourcing Type

```typescript
enum SourcingType {
    JOB_CONFIG = 'job-config',
    ASSET_COL = 'asset-col',
    DATAFRAME = 'dataframe',
}
```

### Sourcing Configs

```typescript
interface ISourcingConfigJobConfig extends ISourcingConfig {
    configSlug: string,
    param: string,
}

interface ISourcingConfigAssetCol extends ISourcingConfig {
    assetSlug: string,
    col: (string | number)[],
}
```

## Service Types

### Query Config

```typescript
interface IQueryConfig {
    serviceName: string,
    queryName: string,
    props: any,
    chunkedParam?: string,
    chunkSize?: number,
    makeDistinct?: boolean,
    categories?: any,
}
```

### API Config

```typescript
interface IApiConfig {
    serviceName: string,
    endpoint: string,
    method: string,
    props: any,
    data: any,
    chunkedParam?: string,
    chunkSize?: number,
}
```

## Status Types

### Data Export Status

```typescript
export enum DataExportStatus {
  WAITING = 'WAITING',
  RUNNING = 'RUNNING',
  IN_PROGRESS = 'IN_PROGRESS',
  COMPLETE = 'COMPLETE',
  ERROR = 'ERROR',
  CANCELLED = 'CANCELLED',
  STALLED = 'STALLED'
}
```

## Storage Types

### Run State

```typescript
export interface IRunState {
    state: any,
    pipeline_config: any,
    storagePath: string,
    dfCache: Map<string, any>,
    partitions: {[key: string]: IPartitonInstance},
    assetRef: Map<string, any>,
}
```

### Partition Instance

```typescript
export interface IPartitonInstance {
    partKey: string,
    field: string,
    filepath?: string,
    partitions: IPartDescription[]
}
```

### Partition Description

```typescript
export interface IPartDescription {
    name: string,
    path: string,
    partSlug: string,
    length: number,
    size: number,
}
```

