# 🏗️ VEA Data Lakehouse DAG Architecture

> **Purpose-built data processing framework for VEA assessment lifecycle data requirements**

This documentation is intended to support developers, engineers, and teams working with the **VEA (Vretta e-Assessment) Data Lakehouse DAG (Directed Acyclic Graph)** system—a data processing and export framework designed to support data requirements at each stage of the VEA assessment lifecycle.

The DAG system handles assessment data from authoring through final data transfers, providing structured data exports, transformations, and integrations tailored to educational assessment workflows. It processes assessment-specific data entities such as test windows, test attempts, student responses, marking windows, and results, enabling comprehensive data exports that align with the six stages of the assessment lifecycle.

## 🚀 Quick Navigation

📖 **[Start here →](./index.md)** - Begin your journey with the main documentation index

## 🎓 Getting Started

New to the VEA Data Lakehouse? Here's how to get started:

1. **Explore the Web UI**: The easiest way to get started is through the [Web UI Guide](./web-ui/index.md), which provides step-by-step tutorials for:
   - [Configuring Assets and Jobs](./web-ui/configure.md) - Define and manage data processing workflows
   - [Running Export Jobs](./web-ui/run.md) - Create and execute data exports

2. **Understand the Architecture**: Read the [main documentation](./index.md) to learn about DAG concepts, assets, jobs, and the overall system architecture

3. **Review the Assessment Lifecycle**: Familiarize yourself with the [six stages of the assessment lifecycle](./assessment-lifecycle.md) to understand how data flows through the system

## 📋 Understanding the Assessment Lifecycle

🔄 **[Assessment Lifecycle →](./assessment-lifecycle.md)** - Learn about the six stages of the VEA assessment process and how the DAG system supports data requirements across all stages

The DAG system is designed to support data needs throughout the assessment lifecycle:
- **Stage 01 (Authoring)**: Export test designs, forms, and item specifications
- **Stage 02 (Registrations)**: Export student, school, and enrollment data
- **Stage 03 (Delivery)**: Export test attempts, sessions, and responses
- **Stage 04 (Marking)**: Export marking windows and scoring data
- **Stage 05 (Analysis)**: Export results and analysis data
- **Stage 06 (Data Transfers)**: Primary focus—comprehensive data exports and integrations

## ✨ What's Inside

- 🔄 **Assessment Lifecycle** - The six stages of VEA assessments and how data flows through them
- 📊 **Data Models** - Asset and job definitions, type systems
- 🔍 **Queries** - Database query documentation
- 🔄 **Transform Functions** - Data transformation operations
- 🌐 **Web Interface** - UI documentation
- 🔌 **Operational APIs** - API reference and usage

## 🎯 Key Features

- ⚡ **DAG-based Architecture** - Efficient data flow through interconnected assets
- 🔀 **Flexible Partitioning** - Numeric and categorical data partitioning
- 🔗 **Dependency Management** - Smart dependency resolution and execution ordering
- 🚦 **Multiple Execution Modes** - Background, foreground, and dev modes
- 📦 **S3 Storage Integration** - Scalable cloud storage for results

---

**Built with ❤️ by the Vretta team**
