# Can You Run Ssis Packages In Fabric

Canonical documentation for Can You Run Ssis Packages In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of this topic is to define the architectural relationship between SQL Server Integration Services (SSIS) and the Microsoft Fabric ecosystem. As organizations transition from traditional on-premises or IaaS-based ETL (Extract, Transform, Load) frameworks to modern SaaS (Software as a Service) data platforms, a bridge is required to maintain legacy logic while leveraging unified data lakes. This documentation addresses the feasibility, methodology, and architectural constraints of executing SSIS workloads within or alongside a Fabric environment.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the structural compatibility of these technologies.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Architectural compatibility between SSIS and Fabric Data Factory.
* The role of Integration Runtimes (IR) in package execution.
* Connectivity patterns between SSIS packages and OneLake.
* Theoretical migration and "lift-and-shift" boundaries.

**Out of scope:**
* Step-by-step UI tutorials for specific software versions.
* Detailed pricing and licensing comparisons.
* Troubleshooting specific third-party SSIS components.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| SSIS Package | A discrete unit of work in SQL Server Integration Services consisting of a collection of tasks, transformations, and data flow components. |
| Microsoft Fabric | An all-in-one analytics solution that covers everything from data movement to data science, Real-Time Analytics, and business intelligence. |
| Integration Runtime (IR) | The compute infrastructure used by Data Factory to provide data integration capabilities across different network environments. |
| Azure-SSIS IR | A fully managed cluster of virtual machines dedicated to running SSIS packages in the cloud. |
| OneLake | The unified, logical data lake for the entire organization, acting as the foundation for all Fabric workloads. |
| Lift-and-Shift | The process of migrating a workload to the cloud with minimal or no changes to the underlying code or architecture. |

## Core Concepts

### 1. Execution vs. Storage
Running SSIS in the context of Fabric requires a distinction between the **execution engine** and the **storage layer**. SSIS is an execution engine. Fabric provides both a modern execution engine (Spark/Data Factory Gen2) and a unified storage layer (OneLake). "Running SSIS in Fabric" typically refers to using Fabric's orchestration capabilities to trigger SSIS packages hosted on compatible compute resources.

### 2. The Managed Bridge
Fabric does not execute SSIS packages natively within its Spark or Data Factory Gen2 engines. Instead, it utilizes the **Azure-SSIS Integration Runtime** (surfaced through Azure Data Factory or linked services) to provide a managed environment where the SSIS engine can operate while interacting with Fabric's data artifacts.

### 3. Connectivity to OneLake
For an SSIS package to be considered "running in Fabric," it must be able to ingest from or load into OneLake. This is achieved via standard protocols (such as ADLS Gen2 APIs) that OneLake supports, allowing SSIS tasks to treat Fabric storage as a compatible destination.

## Standard Model
The standard model for SSIS utilization within a Fabric-centric architecture follows a "Hybrid Orchestration" approach:

1.  **Hosting:** SSIS packages are stored in an SSIS Catalog (SSISDB) hosted on a cloud-based SQL instance or within a package store.
2.  **Compute:** An Azure-SSIS Integration Runtime is provisioned. This compute is linked to the Fabric environment via a Data Factory pipeline.
3.  **Orchestration:** A Fabric Data Factory pipeline uses an "Execute SSIS Package" activity.
4.  **Data Interaction:** The package uses standard connectors (OLE DB, ADO.NET, or specialized Power Query connectors) to interact with Fabric's OneLake or SQL Analytics Endpoints.

## Common Patterns

### Lift-and-Shift
Organizations move existing SSIS packages to the cloud "as-is" to avoid the cost of rewriting logic in Fabric Data Factory Gen2 or Spark. The packages continue to run on the SSIS engine but are managed under the Fabric/ADF umbrella.

### Side-by-Side Coexistence
New transformation logic is developed using Fabric-native tools (Dataflows Gen2, Notebooks), while legacy logic remains in SSIS. Both are orchestrated by the same Fabric pipelines to ensure data consistency.

### OneLake as a Source/Sink
SSIS packages are updated to use OneLake as the primary data repository, effectively using SSIS as a high-performance data movement tool to feed the Fabric ecosystem.

## Anti-Patterns

### Native Execution Fallacy
Attempting to import `.dtsx` files directly into a Fabric Lakehouse or Notebook. SSIS packages require the specific SSIS runtime engine and cannot be interpreted natively by Spark or Fabric's serverless SQL engines.

### Over-Engineering Simple Moves
Using SSIS for simple data movements that Fabric Data Factory Gen2 can handle natively. This adds unnecessary complexity and compute costs associated with maintaining an Integration Runtime.

### Hardcoded Local Paths
Maintaining SSIS packages that rely on local file system paths (e.g., `C:\ETL\Data`). In a Fabric/Cloud context, these must be transitioned to cloud-accessible storage (OneLake/Blob Storage).

## Edge Cases

### Third-Party Components
SSIS packages utilizing custom or third-party DLLs/components require a "Custom Setup" script within the Integration Runtime. If the component is not compatible with a 64-bit cloud environment, the package may fail to run in the Fabric-linked IR.

### On-Premises Connectivity
When SSIS packages must access on-premises data sources while being triggered by Fabric, a Self-Hosted Integration Runtime (SHIR) must be used in conjunction with the Azure-SSIS IR to create a network bridge.

### Legacy Script Tasks
Packages heavily reliant on C# or VB.NET Script Tasks may encounter assembly versioning conflicts when moving from older on-premises SQL Server environments to the managed cloud IR used by Fabric.

## Related Topics
* **Fabric Data Factory Gen2:** The native, low-code data integration service in Fabric.
* **OneLake Shortcuts:** A method to reference data without moving it, which SSIS can utilize via API.
* **Azure Data Factory (ADF):** The foundational technology that provides the SSIS execution capabilities leveraged by Fabric.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |