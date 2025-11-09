# **Microsoft Fabric Governance Dashboard Blueprint (Power BI Design)**

A consultant-grade Power BI dashboard concept to monitor governance, usage, and compliance across Fabric workspaces — ideal for CIOs, Governance Leads, and Data Platform Admins.

---

## **1️⃣ Dashboard Objective**

To provide **real-time visibility** into the health, cost, compliance, and performance of a Microsoft Fabric environment by tracking:

* Governance KPIs (ownership, lineage, access compliance)
* Capacity utilization and cost trends
* Pipeline performance and failure rates
* Security events and RLS coverage
* Documentation and audit readiness

> [!NOTE]
> This dashboard is typically built in **Power BI Direct Lake Mode** using Fabric’s internal logs and Purview exports.

---

## **2️⃣ Data Sources**

| Source                          | Type               | Description                           | Refresh Mode      |
| ------------------------------- | ------------------ | ------------------------------------- | ----------------- |
| **Fabric Capacity Metrics App** | API                | CU usage, job runtimes, user activity | Direct Lake       |
| **Purview Lineage Export**      | CSV / API          | Dataset lineage and ownership mapping | Scheduled (daily) |
| **Fabric Admin API**            | JSON               | Workspace and dataset metadata        | API refresh       |
| **Data Factory Pipeline Logs**  | Delta              | Run times, success/failure rates      | Direct Lake       |
| **Power BI Audit Logs**         | CSV / API          | Access events, report sharing         | Daily             |
| **Defender for Cloud**          | API                | Security alerts and compliance events | Daily             |
| **Change Log Repository**       | SharePoint / Delta | Change and incident register          | Direct Lake       |

> [!TIP]
> Store all logs in OneLake under `/Governance/Monitoring/` for unified access across teams.

---

## **3️⃣ Data Model (Simplified)**

```text
Workspaces 1 ──── * Datasets
Datasets 1 ──── * Pipelines
Pipelines 1 ──── * PipelineRuns
Workspaces 1 ──── * AccessLogs
Workspaces 1 ──── * CostMetrics
Datasets 1 ──── * LineageEntries
SecurityPolicies 1 ──── * Users
```

> [!IMPORTANT]
> Maintain **dimensional consistency**: Workspace, User, and Dataset should serve as primary dimensions across all fact tables.

---

## **4️⃣ Dashboard Pages and KPIs**

### **Page 1: Governance Overview**

**Visuals**

* **Ownership Coverage (Gauge):** % of datasets with assigned owner
* **Lineage Completeness (Bar):** % datasets traced end-to-end
* **RLS/OLS Enforcement (Card):** Workspaces with active RLS policies
* **Governance Score (Composite KPI):** Weighted index of 5 governance metrics
* **Data Sensitivity Matrix (Heatmap):** High / Medium / Low classification by domain

**Sample DAX:**

```DAX
Ownership Coverage % = 
DIVIDE(COUNTROWS(OwnedDatasets), COUNTROWS(AllDatasets)) * 100
```

> [!NOTE]
> Governance Score can be a composite index = (Ownership + Lineage + RLS + Documentation) / 4.

---

### **Page 2: Capacity & Cost Monitoring**

**Visuals**

* **CU Utilization (Line Chart):** Hourly / daily CU consumption
* **Workspace Cost Breakdown (Treemap):** CU usage by department
* **Idle Capacity (Card):** % of underutilized compute
* **Cost Forecast (Line Chart):** 30-day trend with moving average

**Sample DAX:**

```DAX
CU Utilization % =
DIVIDE(SUM(FabricCapacity[UsedCU]), SUM(FabricCapacity[AllocatedCU])) * 100
```

> [!TIP]
> Highlight workspaces exceeding **80% average CU** as candidates for scaling or optimization.

---

### **Page 3: Pipeline Health**

**Visuals**

* **Pipeline Success Rate (Donut):** Success vs Failure count
* **Avg Run Duration (Bar):** Per workspace or per pipeline
* **Failed Runs (Table):** Pipeline name, error code, last run time
* **Failure Trend (Line):** Week-over-week error frequency

**Sample DAX:**

```DAX
Pipeline Success Rate = 
DIVIDE(COUNTROWS(FILTER(Pipelines, Pipelines[Status] = "Success")),
       COUNTROWS(Pipelines)) * 100
```

> [!CAUTION]
> If success rate < 95%, schedule review of transformations and Spark job logs.

---

### **Page 4: Security & Access**

**Visuals**

* **Active Users (Card):** Distinct users in last 7 days
* **Access Violations (Table):** Users accessing unauthorized datasets
* **RLS Coverage (Gauge):** % of datasets with RLS enabled
* **Defender Alerts (Stacked Bar):** Alert types (data exfiltration, permission abuse, etc.)

**Sample DAX:**

```DAX
RLS Coverage % = 
DIVIDE(COUNTROWS(FILTER(Datasets, Datasets[RLS_Enabled] = TRUE())),
       COUNTROWS(Datasets)) * 100
```

> [!IMPORTANT]
> Use Power BI’s **Object-Level Security (OLS)** metadata for full access coverage validation.

---

### **Page 5: Compliance & Documentation**

**Visuals**

* **Documentation Currency (Card):** % of datasets updated <30 days ago
* **Audit Log Volume (Line):** Events logged per day
* **Change Log (Table):** Date, Object, Change Type, Owner
* **Policy Compliance Heatmap:** Workspaces vs. policy checklists

**Sample DAX:**

```DAX
Doc Currency % =
DIVIDE(COUNTROWS(FILTER(Datasets, DATEDIFF(Datasets[LastUpdated], TODAY(), DAY) <= 30)),
       COUNTROWS(Datasets)) * 100
```

> [!NOTE]
> Add conditional formatting to highlight outdated documents (>60 days old).

---

### **Page 6: Governance KPIs Dashboard**

| Metric                    | Target | Actual | Status |
| ------------------------- | ------ | ------ | ------ |
| Data Ownership Coverage   | 100%   | 94%    | ⚠️     |
| Lineage Completeness      | 95%    | 97%    | ✅      |
| RLS Compliance            | 100%   | 100%   | ✅      |
| CU Utilization Efficiency | <80%   | 72%    | ✅      |
| Documentation Currency    | 100%   | 85%    | ⚠️     |
| Pipeline Success Rate     | >98%   | 96%    | ⚠️     |

> [!TIP]
> Use conditional icons: ✅ = Meets, ⚠️ = Watch, ❌ = Needs attention.

---

## **5️⃣ Advanced Features**

### **Alerting**

* Integrate with **Power Automate** to trigger Teams or email notifications for:

  * CU utilization > 90%
  * Pipeline failure count > threshold
  * Expired documentation (>60 days)
  * Security alert severity = High

### **Drillthrough**

* Drillthrough from Workspace summary → Dataset lineage → Pipeline runs → User access logs.

### **Bookmark Navigation**

* Add top-level bookmarks: *Governance*, *Security*, *Cost*, *Pipelines*, *Audit*.

### **Role-Based Access**

* Apply **RLS**:

  * *CIO*: Full view
  * *Governance Lead*: Compliance + documentation
  * *Fabric Admin*: Capacity + pipelines
  * *Data Steward*: Lineage + ownership

---

## **6️⃣ Recommended Dashboard Layout**

```text
╔══════════════════════════════════════════════╗
║                Header Bar                    ║
║  [Fabric Governance Dashboard]  [Date Filter]║
╠══════════════════════════════════════════════╣
║  [Governance Score]   [Ownership Gauge]      ║
║  [RLS Coverage]       [Lineage Bar Chart]    ║
╠══════════════════════════════════════════════╣
║  [CU Usage Trend]  [Cost Treemap]  [Alerts]  ║
╠══════════════════════════════════════════════╣
║  [Pipeline Health Trend]  [Failed Jobs Table]║
╠══════════════════════════════════════════════╣
║  [Compliance Heatmap]   [Documentation Cards]║
╚══════════════════════════════════════════════╝
```

> [!TIP]
> Use **neutral palette (gray-blue)** with accent color for alerts (orange/red).
> Keep no more than **12 visuals per page** for performance.

---

## **7️⃣ Example Metrics Table for BI Team**

| Metric Name          | Description                  | Source Table     | DAX Measure                              |
| -------------------- | ---------------------------- | ---------------- | ---------------------------------------- |
| `OwnershipCoverage%` | Datasets with assigned owner | Datasets         | `DIVIDE(COUNT(Owner), COUNT(DatasetID))` |
| `RLSCoverage%`       | Datasets with RLS applied    | SecurityPolicies | `COUNT(Filter(...)) / COUNT(...)`        |
| `CUEfficiency%`      | Utilization ratio            | CapacityMetrics  | `UsedCU / AllocatedCU`                   |
| `PipelineSuccess%`   | Success rate                 | PipelineRuns     | `COUNT(Success) / COUNT(AllRuns)`        |
| `DocCurrency%`       | Datasets updated <30 days    | Documentation    | `DATEDIFF(LastUpdate, TODAY(), DAY)`     |

---

## **8️⃣ Deployment and Maintenance**

* [ ] Store `.pbix` in GitHub/DevOps with semantic model JSON.
* [ ] Publish to Fabric workspace “**Governance Monitor**”.
* [ ] Schedule refresh daily (Direct Lake preferred).
* [ ] Assign Viewer roles to Leadership, Editors to Governance team.
* [ ] Archive monthly snapshots to `/OneLake/Governance/Dashboards/`.

---

## **9️⃣ Strategic Value**

> [!IMPORTANT]
> This Governance Dashboard becomes the **CIO’s single pane of glass** for Fabric oversight.
> It helps justify budget allocation, monitor SLA adherence, and ensure **compliance with data trust frameworks** (ISO 27001, SOC 2).

---

Would you like me to create a **Power BI semantic model layout** next (list of dimensions, facts, relationships, and key DAX measures for this Governance Dashboard)?
