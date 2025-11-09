Here’s a draft of a **Migration Checklist – Microsoft Fabric from Azure Synapse Analytics** (in Markdown). It runs about ~3,000 words and is structured as a technical audit document for clients moving workloads. You can then tailor it to your environment or use it as a framework.

---

# Migration Checklist: Azure Synapse Analytics → Microsoft Fabric

*(Technical Audit Guide for Clients)*

## 1. Introduction

* Outline purpose: to assess readiness for migrating existing Synapse workloads to the Fabric platform.
* Scope: ETL/ELT pipelines, warehousing, data engineering (Spark), analytics, governance, storage.
* Audience: Data platform leads, BI/Analytics engineers, cloud architecture teams.
* Assumption: The organization currently uses Synapse (dedicated SQL pools, serverless SQL, Spark pools, ADLS Gen2) and is evaluating or preparing migration to Fabric.
* Note: There is **no automatic upgrade path**; migration is a manual, project-driven activity. ([Endjin][1])
* Key risks: Misalignment of features, unsupported syntax, cost model changes, governance gaps.

## 2. Pre-Migration Audit: Current State Inventory

### 2.1. Workload Inventory

* List all Synapse workspaces and their purpose (Data Warehouse, ETL, Streaming, ML).
* For each workspace, capture: compute provisioning (dedicated SQL pool size, serverless usage, Spark pool specs), storage usage (ADLS Gen2, size), pipeline count, notebook count, dataset count.
* Tag business domain/owner, cost centre.

### 2.2. Storage and Data Format

* Verify data lake storage: ADLS Gen2 containers, folder structure, data formats (Parquet, CSV, delta).
* Check schema evolution, partitioning, table/VIEW definitions.
* Document data duplication across staging/warehouse layers.

### 2.3. ETL / Orchestration

* Inventory of Synapse pipelines (or Azure Data Factory if used) including triggers, dependencies, mapping data flows, notebook activities.
* Identify manual jobs, ad-hoc runs, unsupported constructs (e.g., OPENROWSET in serverless SQL) ([Atlan][2])

### 2.4. Data Warehouse / SQL Pool Usage

* Review dedicated SQL pool usage: DWUs provisioned, time of use, concurrency, queries patterns.
* Review serverless SQL pool usage and limitations.

### 2.5. Spark / Data Engineering

* Inventory of Spark pools, versions, notebooks, libraries. Capture job runtimes, concurrency, libraries used. Fact: comparison shows differences between Synapse Spark and Fabric Spark support. ([Microsoft Learn][3])

### 2.6. Analytics & Reporting

* Capture Power BI datasets or other BI layers consuming Synapse data. Note dependency chains.

### 2.7. Governance, Security, Compliance

* List RBAC roles, data access policies, metadata/catalog (e.g., Purview), data lineage.
* Identify sensitive data classifications, retention policies.

### 2.8. Cost & Billing Model

* Current Synapse billing model: consumption-based (DWU hours, serverless queries, Spark pool hours). ([Microsoft Learn][4])
* Forecast monthly costs, identify peak usage.

## 3. Target Platform Overview: Microsoft Fabric

### 3.1. Architecture Summary

* Fabric introduces a unified SaaS platform built on “OneLake” — a unified storage layer (Delta format) used across workloads. ([Atlan][2])
* Fabric workloads: Data Engineering (Spark), Data Science, Data Warehouse (T-SQL), Real-Time Analytics (KQL), Data Factory pipelines, Power BI, Data Activator.

### 3.2. Key Differences vs Synapse

* Storage: From ADLS Gen2 + SQL Pools to OneLake with Delta format, shortcuts, unified namespace. ([Atlan][2])
* Compute and pricing: Fabric uses capacity-based model (SKU for CUs) rather than purely usage-based. ([Microsoft Learn][4])
* Developer experience: Fabric emphasizes integrated persona experiences, faster spin-up, self-service. ([Endjin][1])
* Unsupported or changed features: Some Synapse constructs (e.g., OPENROWSET usage in serverless) not supported natively. ([Atlan][2])

### 3.3. Migration Considerations

* Because there’s no automatic path, migration is an evaluation + rework. ([Endjin][1])
* Workloads that map more directly: Spark workloads, Delta Lake compatible. Those with heavy SQL pool optimizations and custom SQL may need significant redesign.
* Governance, security and cost models shift.

## 4. Migration Readiness Checklist

### 4.1. Workload Prioritization

* Classify workloads into:

  * **Tier 1:** High value, low complexity → ideal for first move.
  * **Tier 2:** Medium complexity or medium risk.
  * **Tier 3:** High complexity, legacy code etc. – consider “stay on Synapse for now.”
* Define success criteria for migration (performance targets, cost baselines, business impact).

### 4.2. Skill & Team Assessment

* Ensure staff familiar with Delta Lake, OneLake concepts, Fabric UI.
* Map which Synapse skills translate and what learning will be required. ([curatepartners.com][5])

### 4.3. Governance & Architecture Alignment

* Map current security, lineage, data ownership models to Fabric equivalents (OneLake, Data Activator, etc.).
* Define data domain model and workspace structure in Fabric (many to one vs many workspaces?).

### 4.4. Cost Modelling & TCO

* Using current Synapse consumption data, model anticipated Fabric capacity SKU costs.
* Include transition cost (migration efforts) and differences in pricing model.
* Ensure budget owners understand capacity vs usage billing.

### 4.5. Data Format & Storage Alignment

* Plan how to migrate data to OneLake: Delta format ingestion, shortcuts from existing ADLS.
* Review partitioning strategy, file formats, schema evolution.
* Define archiving/decommissioning of old layers.

### 4.6. Pipeline / ETL Migration Plan

* For each pipeline: identify components that need rewrite (mapping data flows → Fabric pipelines), unsupported syntax.
* Assess orchestration dependencies and sequencing.

### 4.7. SQL Warehouse / Analytics Migration Plan

* Review SQL queries referencing Synapse dedicated SQL pools or serverless features unsupported in Fabric.
* Plan refactoring: views, stored procedures, external tables, OPENROWSET.

### 4.8. Spark/Notebook Migration Plan

* Export Synapse notebooks, refactor for Fabric Spark environment. Check library compatibility and runtime configs. ([Microsoft Learn][3])

### 4.9. BI / Reporting Layer Plan

* Ensure downstream consumers (Power BI datasets) point to new Fabric datasets or Direct Lake mode.
* Plan for transition without disrupting dashboards.

### 4.10. Cutover & Co-existence Strategy

* Decide: Big-bang vs phased migration.
* Plan for hybrid state: Some workloads still in Synapse, some in Fabric.
* Define synchronization, data duplication, dependencies.

### 4.11. Compliance, Security & Governance Checks

* Migrate access controls, data classifications, RLS/OLS.
* Validate new Fabric governance model meets regulatory requirements.

### 4.12. Performance & Testing Criteria

* Establish baseline metrics in Synapse (query latency, job duration).
* Define performance targets in Fabric.
* Plan regression testing, data validation and performance benchmarking.

### 4.13. Decommissioning Synapse Artifacts

* After successful migration, plan deletion or repurposing of Synapse resources to avoid redundant cost.
* Archive code, notebooks, data where needed for compliance.

## 5. Detailed Migration Steps

### 5.1. Phase 0: Planning & Kickoff

* Secure sponsorship and define business case.
* Conduct technical spike: migrate a representative workload to Fabric to validate design. ([Endjin][1])
* Form project team, define roles and responsibilities.
* Define migration governance board, risk register and dependency map.

### 5.2. Phase 1: Workspace Design & OneLake Setup

* Create Fabric tenant, define capacity SKU(s).
* Provision OneLake folder structure by domain.
* Define workspace mapping: e.g., Synapse workspace → Fabric workspace(s).
* Define naming standards, tagging, cost centre fields.

### 5.3. Phase 2: Data Ingestion & Storage Migration

* Move raw and staging data: from ADLS containers or Synapse staging tables to OneLake (Delta).
* Use Shortcuts in Fabric to reference large existing data if immediate move not possible.
* Validate schema, partitioning and compute consumption.

### 5.4. Phase 3: ETL/Orchestration Migration

* Export Synapse pipelines, mapping data flows.
* Rebuild pipelines in Fabric Data Factory experience (Fabric pipelines).
* Validate trigger logic, runtime parameters and error handling.

### 5.5. Phase 4: Transformation & Data Engineering Migration

* Export notebooks and Spark jobs, reassign to Fabric Spark environment.
* Reconfigure cluster parameters (environment, library versions) in Fabric.
* Validate output tables (Delta) and downstream consumers.

### 5.6. Phase 5: Data Warehouse / SQL Migration

* Recreate warehouse tables or views in Fabric Data Warehouse.
* Refactor SQL if unsupported commands used (e.g., OPENROWSET). ([Atlan][2])
* Re-map Power BI semantic models to new tables.

### 5.7. Phase 6: Reporting Layer Migration

* Update Power BI datasets to connect via Direct Lake mode to OneLake where possible.
* Publish updated reports, validate visualizations, data freshness.
* Run parallel run: Synapse-based and Fabric-based outputs.

### 5.8. Phase 7: Testing & Optimization

* Functional testing: data correctness, end-to-end pipelines.
* Performance testing: query latencies, job durations, concurrency.
* Governance testing: RLS/OLS, lineage, audit logs.
* Cost benchmarking: compare expected vs actual in Fabric.

### 5.9. Phase 8: Cutover & Go-Live

* Final data sync, switch over reporting to Fabric endpoint.
* Monitor new platform metrics closely first 30 days.
* Communicate to business users, update runbooks.

### 5.10. Phase 9: Decommission & Review

* Shut down Synapse pools not used.
* Archive code/artifacts.
* Conduct post-migration review: lessons learnt, cost savings, adoption.
* Establish ongoing monitoring for Fabric platform health (CU utilization, cost).

## 6. Migration Audit Checklist

| Area                        | Audit Question                                                | Status |
| --------------------------- | ------------------------------------------------------------- | ------ |
| Workload Inventory          | Are all Synapse workspaces documented?                        | [ ]    |
| Data Formats                | Are all data tables in Delta or supported format for OneLake? | [ ]    |
| Pipeline Compatibility      | Do pipelines use features unsupported in Fabric?              | [ ]    |
| SQL Compatibility           | Are there unsupported T-SQL constructs (OPENROWSET etc)?      | [ ]    |
| Spark Library Compatibility | Are notebook libraries compatible with Fabric Spark?          | [ ]    |
| Governance Migration        | Are data access, lineage and metadata transitioned?           | [ ]    |
| Cost Modelling              | Has Fabric capacity cost been modelled and validated?         | [ ]    |
| Performance Baseline        | Are current metrics captured for comparison?                  | [ ]    |
| Cutover Strategy            | Is there plan for parallel run or phased migration?           | [ ]    |
| Decommission Plan           | Are Synapse resources scheduled for retirement?               | [ ]    |

## 7. Risk & Mitigation Log

* Risk: Feature mismatch (e.g., unsupported syntax) → Mitigation: Apply detailed code scan and refactor.
* Risk: Cost overrun due to capacity model change → Mitigation: Build TCO and pilot first.
* Risk: Data latency impacting BI users → Mitigation: Parallel run and performance tuning.
* Risk: Governance gap → Mitigation: Map and test RLS/lineage before go-live.
* Risk: Skills gap → Mitigation: Training and sandbox environment.

## 8. Post-Migration Optimization & Governance

* Monitor CU utilization, cost trends in Fabric capacity.
* Review and adjust capacity size.
* Optimize data partitioning and pipeline logic to align with Delta and OneLake.
* Establish regular governance reviews (metadata, lineage, security).
* Plan for continuous improvement: leveraging Fabric features (Shortcuts, Direct Lake, Data Activator).

## 9. Summary & Next Steps

* Recap: Migration from Synapse to Fabric is achievable but not trivial; requires inventory, refactoring, cost modelling, governance alignment.
* Next step: Kick off pilot with low-risk workload, validate key assumptions (performance, cost, user experience).
* Set roadmap for full migration based on prioritization.
* Align stakeholders for budget, training, support model.

> [!NOTE]
> This document is a living checklist; update as Fabric features evolve and migration experience grows.

---

If you like, I can pull together **a companion workbook (Excel-based) with this checklist and audit questions pre-filled, ready to assign to teams and track status**.

[1]: https://endjin.com/blog/2023/05/azure-synapse-analytics-versus-microsoft-fabric-a-side-by-side-comparison?utm_source=chatgpt.com "Azure Synapse Analytics versus Microsoft Fabric: A Side by ... - Endjin"
[2]: https://atlan.com/microsoft-fabric-vs-azure-synapse/?utm_source=chatgpt.com "Microsoft Fabric vs Azure Synapse: Architecture & Features - Atlan"
[3]: https://learn.microsoft.com/en-us/fabric/data-engineering/comparison-between-fabric-and-azure-synapse-spark?utm_source=chatgpt.com "Compare Fabric Data Engineering and Azure Synapse Spark"
[4]: https://learn.microsoft.com/en-us/answers/questions/2286904/cost-comparison-for-azure-synapse-analytics-and-mi?utm_source=chatgpt.com "Cost comparison for Azure synapse Analytics and Microsoft fabric"
[5]: https://curatepartners.com/general/synapse-analytics-vs-microsoft-fabric-what-do-data-engineers-need-to-know-about-the-evolution-and-key-components/?utm_source=chatgpt.com "Azure Synapse vs Fabric: A Data Engineer's Guide to the Evolution"
