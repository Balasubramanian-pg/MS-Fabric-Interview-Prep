# **Power BI Semantic Model Layout – Fabric Governance Dashboard**

A structured model blueprint to help consultants and BI teams design the **semantic layer** for the *Fabric Governance Dashboard*.
It aligns all monitoring, compliance, and capacity datasets into a star schema optimized for Direct Lake or Import mode.

---

## **1️⃣ Core Design Principles**

* Use a **star schema** — all fact tables link through conformed dimension tables (`Workspace`, `User`, `Dataset`, etc.).
* Apply **consistent workspace IDs** across all Fabric exports and APIs.
* Enforce **role-based RLS** for CIO, Admin, and Governance teams.
* Keep **granularity per dataset/workspace/day** for accurate trend analysis.

> [!TIP]
> Always normalize Fabric Admin API and Purview outputs before loading; nested JSON responses cause DAX calculation issues.

---

## **2️⃣ Dimensions (Lookup Tables)**

| Dimension             | Description                                       | Key Columns                                                               | Notes                                   |
| --------------------- | ------------------------------------------------- | ------------------------------------------------------------------------- | --------------------------------------- |
| **DimWorkspace**      | List of Fabric workspaces and capacities          | `WorkspaceID`, `WorkspaceName`, `CapacityID`, `Department`, `Environment` | OneLake folder path as optional key     |
| **DimUser**           | All Fabric and Power BI users                     | `UserID`, `UserName`, `Email`, `Role`, `Region`                           | Join via UserID to Access or Audit Logs |
| **DimDataset**        | All datasets registered in Fabric / Power BI      | `DatasetID`, `DatasetName`, `OwnerID`, `Domain`, `SensitivityLabel`       | Imported via Fabric Admin API           |
| **DimPipeline**       | Data Factory and Synapse pipelines                | `PipelineID`, `PipelineName`, `WorkspaceID`, `OwnerID`                    | Links to FactPipelineRuns               |
| **DimSecurityPolicy** | RLS/OLS policies and roles                        | `PolicyID`, `PolicyName`, `DatasetID`, `AppliedTo`                        | Used for RLS coverage KPIs              |
| **DimDate**           | Standard calendar table                           | `DateKey`, `Date`, `Month`, `Quarter`, `Year`, `IsWeekend`                | Basis for daily aggregations            |
| **DimAlertType**      | Categories for Defender and Data Activator alerts | `AlertID`, `AlertCategory`, `Severity`, `Source`                          | Mapped to FactSecurityEvents            |

> [!NOTE]
> Maintain a **shared DimDate and DimUser** across all governance dashboards for consistency.

---

## **3️⃣ Fact Tables**

| Fact Table                  | Grain                  | Description                        | Key Columns                                                                         | Source                      |
| --------------------------- | ---------------------- | ---------------------------------- | ----------------------------------------------------------------------------------- | --------------------------- |
| **FactCapacityUsage**       | Workspace + Day        | CU usage, memory, cost             | `WorkspaceID`, `DateKey`, `UsedCU`, `AllocatedCU`, `CostINR`                        | Fabric Capacity Metrics App |
| **FactPipelineRuns**        | Pipeline + Day         | ETL execution counts and durations | `PipelineID`, `WorkspaceID`, `RunStatus`, `DurationMinutes`, `StartTime`, `EndTime` | Data Factory Run Logs       |
| **FactDatasetOwnership**    | Dataset + Owner        | Dataset ownership and sensitivity  | `DatasetID`, `OwnerID`, `SensitivityLabel`, `Classification`                        | Purview Export              |
| **FactLineageCompleteness** | Dataset + Source       | Lineage depth and completeness %   | `DatasetID`, `SourceCount`, `TracedCount`, `Completeness%`                          | Purview Lineage             |
| **FactSecurityEvents**      | Workspace + Alert      | Security incidents, RLS coverage   | `WorkspaceID`, `AlertID`, `Severity`, `Status`, `ResolvedBy`                        | Defender for Cloud          |
| **FactDocumentation**       | Dataset + Date         | Last updated documentation status  | `DatasetID`, `LastUpdated`, `DocStatus`                                             | SharePoint Metadata         |
| **FactAccessLogs**          | User + Workspace + Day | User actions, access counts        | `UserID`, `WorkspaceID`, `AccessCount`, `AccessType`                                | Power BI Audit Logs         |

> [!IMPORTANT]
> Each Fact table must include **WorkspaceID** and **DateKey** to enable unified filtering across pages.

---

## **4️⃣ Relationships Schema (Star Layout)**

```text
DimWorkspace ──< FactCapacityUsage  >── DimDate
DimWorkspace ──< FactPipelineRuns   >── DimDate
DimWorkspace ──< FactSecurityEvents >── DimDate
DimDataset   ──< FactDatasetOwnership
DimDataset   ──< FactLineageCompleteness
DimDataset   ──< FactDocumentation
DimPipeline  ──< FactPipelineRuns
DimUser      ──< FactAccessLogs
DimSecurityPolicy ──< FactSecurityEvents
```

* One-to-many from all dimensions to corresponding fact tables.
* Shared filters: WorkspaceID, DateKey, DatasetID.

---

## **5️⃣ Key Measures (DAX Library)**

### **Governance Metrics**

```DAX
Ownership Coverage % =
DIVIDE(COUNTROWS(FILTER(FactDatasetOwnership, NOT(ISBLANK(FactDatasetOwnership[OwnerID])))),
       COUNTROWS(FactDatasetOwnership)) * 100

Lineage Completeness % =
AVERAGEX(FactLineageCompleteness, FactLineageCompleteness[Completeness%])

RLS Coverage % =
DIVIDE(COUNTROWS(FILTER(DimSecurityPolicy, DimSecurityPolicy[PolicyType] = "RLS")),
       COUNTROWS(DimDataset)) * 100
```

---

### **Capacity & Cost**

```DAX
CU Utilization % =
DIVIDE(SUM(FactCapacityUsage[UsedCU]), SUM(FactCapacityUsage[AllocatedCU])) * 100

Cost (Monthly INR) =
SUMX(FactCapacityUsage, FactCapacityUsage[CostINR])
```

> [!TIP]
> Create a calculated table `FactCapacityTrend` to display moving averages for forecasting.

---

### **Pipeline Health**

```DAX
Pipeline Success Rate % =
DIVIDE(COUNTROWS(FILTER(FactPipelineRuns, FactPipelineRuns[RunStatus] = "Success")),
       COUNTROWS(FactPipelineRuns)) * 100

Avg Pipeline Duration (min) =
AVERAGE(FactPipelineRuns[DurationMinutes])
```

---

### **Compliance Metrics**

```DAX
Documentation Currency % =
DIVIDE(COUNTROWS(FILTER(FactDocumentation,
    DATEDIFF(FactDocumentation[LastUpdated], TODAY(), DAY) <= 30)),
    COUNTROWS(FactDocumentation)) * 100

Security Incidents (Open) =
COUNTROWS(FILTER(FactSecurityEvents, FactSecurityEvents[Status] = "Open"))
```

---

### **User & Access Metrics**

```DAX
Active Users (7D) =
CALCULATE(DISTINCTCOUNT(FactAccessLogs[UserID]),
          DATESINPERIOD(DimDate[Date], TODAY(), -7, DAY))

Unauthorized Access % =
DIVIDE(COUNTROWS(FILTER(FactAccessLogs, FactAccessLogs[AccessType] = "Denied")),
       COUNTROWS(FactAccessLogs)) * 100
```

---

## **6️⃣ Sample Hierarchies**

| Dimension    | Hierarchy                        | Example Use            |
| ------------ | -------------------------------- | ---------------------- |
| DimDate      | Year → Quarter → Month → Day     | Timeline analysis      |
| DimWorkspace | Department → Workspace → Dataset | Departmental CU trends |
| DimUser      | Role → Region → UserName         | RLS by region          |
| DimDataset   | Domain → Dataset → Sensitivity   | Governance heatmap     |
| DimPipeline  | Workspace → Pipeline             | Failure monitoring     |

> [!NOTE]
> Use **role-based slicers** (e.g., by department or data owner) for personalized dashboards.

---

## **7️⃣ Role-Level Security (RLS) Plan**

| Role                | Access Scope                               | Filter Expression Example                                |
| ------------------- | ------------------------------------------ | -------------------------------------------------------- |
| **CIO**             | All workspaces                             | None (Full Access)                                       |
| **Governance Lead** | All governance metrics, restricted to cost | `[FactCapacityUsage][Department] IN {"Finance","IT"}`    |
| **Fabric Admin**    | Technical metrics only                     | `[FactCapacityUsage][WorkspaceID] = USERPRINCIPALNAME()` |
| **Data Steward**    | Datasets they own                          | `[DimDataset][OwnerID] = USERPRINCIPALNAME()`            |

> [!CAUTION]
> Test all RLS roles using *View as Role* in Power BI Service before production deployment.

---

## **8️⃣ Semantic Model Parameters**

| Parameter             | Default Value | Purpose                         |
| --------------------- | ------------- | ------------------------------- |
| `RefreshFrequency`    | Daily         | Frequency for scheduled refresh |
| `DateRange`           | Last 90 Days  | Limits historical data volume   |
| `IncludeSecurityData` | TRUE          | Toggles Defender data inclusion |
| `Environment`         | “Prod”        | Filters by workspace type       |

> [!TIP]
> Parameters help optimize refresh and manage dev/test isolation.

---

## **9️⃣ Recommended Calculated Columns**

| Table                | Column                | Expression                                      |
| -------------------- | --------------------- | ----------------------------------------------- |
| `FactCapacityUsage`  | `UtilizationCategory` | `IF([CU Utilization %] > 80, "High", "Normal")` |
| `FactPipelineRuns`   | `RunCategory`         | `IF([DurationMinutes] > 60, "Long", "Short")`   |
| `FactSecurityEvents` | `IsCritical`          | `IF([Severity] = "High", TRUE(), FALSE())`      |
| `FactDocumentation`  | `IsOutdated`          | `DATEDIFF([LastUpdated], TODAY(), DAY) > 30`    |

---

## **10️⃣ Dashboard KPIs Mapping**

| KPI                       | Source Measure              | Visual Type        |
| ------------------------- | --------------------------- | ------------------ |
| Governance Score          | Weighted index of 4 metrics | Card               |
| Ownership Coverage %      | DAX Measure                 | Gauge              |
| RLS Coverage %            | DAX Measure                 | Donut              |
| CU Utilization %          | DAX Measure                 | Line chart         |
| Pipeline Success %        | DAX Measure                 | Column chart       |
| Security Incidents (Open) | DAX Measure                 | Table              |
| Documentation Currency %  | DAX Measure                 | Bar                |
| Cost (Monthly INR)        | DAX Measure                 | Combo (bar + line) |

> [!NOTE]
> Each KPI visual should link via drillthrough to the relevant workspace-level detail page.

---

## **11️⃣ Refresh and Maintenance Strategy**

* [ ] Use **Direct Lake** where possible for real-time CU and pipeline metrics.
* [ ] For API data (Purview, Defender), schedule **daily refresh**.
* [ ] Refresh order: `Dimensions → Fact Tables → Measures`.
* [ ] Store `.pbix` in GitHub/DevOps for version control.
* [ ] Audit refresh duration weekly for optimization.

---

## **12️⃣ Deliverable Summary**

✅ Star schema with governance and cost metrics
✅ Standardized dimension model (Workspace, Dataset, User)
✅ DAX library of 15+ core measures
✅ RLS configuration by persona
✅ Drillthrough and cross-filter enabled dashboard
✅ Direct Lake + API hybrid data model

---

Would you like me to wrap this entire Fabric Governance module (Governance Framework + Dashboard + Semantic Model) into a **consultant-style implementation playbook outline** — basically the structure of a deliverable deck or client report (with chapters and executive summary)?
