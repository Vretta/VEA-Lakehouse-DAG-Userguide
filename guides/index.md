# Data Lakehouse DAG Guides

This directory contains practical guides for developing and working with the Data Lakehouse DAG system.

## Available Guides

### [Code Review Checklist](./code-review-checklist.md)

A comprehensive checklist for reviewing Data Lakehouse DAG assets, queries, and transforms. Ensures correct configuration of required inputs, dependency sourcings, query SQL validation, and asset schemas. Includes common errors to avoid and testing guidelines.

### [Partitioning Guide](./partitioning-guide.md)

Comprehensive guide to using partitioning effectively for memory management and large-scale data processing. Covers numeric and categorical partitioning, configuration patterns, best practices, troubleshooting, and decision trees. See also [Partitioning Examples](./partitioning-examples.md) for practical code examples.

### [Partitioning Examples](./partitioning-examples.md)

Practical code examples demonstrating partitioning patterns and configurations. Includes examples of numeric partitioning, categorical partitioning, repartitioning, and common use cases.

### [Query Reuse Guidelines](./query-reuse-guidelines.md)

Guidelines for reusing existing queries before creating new ones. Includes the query reuse process, common patterns, schema design best practices (avoiding JSON columns), and examples of good query reuse.

### [Prompt Examples](./prompt-examples.md)

Prompt templates and examples for developing and extending the Data Lakehouse DAG system. Includes templates for creating transforms, assets, jobs, debugging, and integration with the web client.

### [Specification Reference](./specification-reference.md)

Quick reference guide pointing to specification-only documentation needed for writing code. Maps specifications to source file locations and provides development workflows for creating transforms, assets, and jobs.

