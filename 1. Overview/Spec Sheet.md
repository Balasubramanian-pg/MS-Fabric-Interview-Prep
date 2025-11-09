Perfect — here’s the **Power BI Developer Spec Sheet** for the **Fabric Cost Governance Dashboard**.
It’s written like a handoff document you’d give your BI developer team, with precise visual types, data bindings, and layout guidance.

---

# **Microsoft Fabric Cost Governance Dashboard – Power BI Developer Spec Sheet**

---

## **1️⃣ General Info**

| Item                 | Detail                                                                      |
| -------------------- | --------------------------------------------------------------------------- |
| **Dashboard Name**   | Microsoft Fabric Cost Governance Dashboard                                  |
| **Purpose**          | Track CU utilization, cost efficiency, job performance, and capacity health |
| **Data Source**      | Fabric Capacity Metrics App + OneLake Custom Cost Table                     |
| **Refresh Mode**     | Direct Lake (preferred) or Import (for archived metrics)                    |
| **Workspace**        | Governance_Monitor                                                          |
| **Expected Users**   | CIO, Finance Controller, Fabric Admin, Data Engineers                       |
| **Update Frequency** | Daily (API + metrics app refresh)                                           |

---

## **2️⃣ Dataset Tables and Relationships**

**Fact Tables**

* `FactCapacityUsage` – CU usage by hour/workspace
* `FactJobExecutions` – job durations and types
* `FactCostBudget` – departmental budgets
* `FactWorkspaceTagging` – department and owner mapping

**Dimension Tables**

* `DimWorkspace` – workspace metadata
* `DimDate` – calendar
* `DimCostCenter` – cost mapping

**Relationships**

* `FactCapacityUsage[WorkspaceID] → DimWorkspace[WorkspaceID]`
* `FactCapacityUsage[DateKey] → DimDate[DateKey]`
* `DimWorkspace[CostCenter] → DimCostCenter[CostCenter]`

---

## **3️⃣ Core Measures (DAX)**

```DAX
CU Utilization % =
DIVIDE(SUM(FactCapacityUsage[UsedCU]), SUM(FactCapacityUsage[AllocatedCU])) * 100

Idle Hours =
SUMX(FactCapacityUsage, IF(FactCapacityUsage[UsedCU] = 0, 1, 0))

Cost per Workspace (INR) =
SUM(FactCapacityUsage[CostINR])

CU Efficiency Index =
(100 - [Idle Hours]) * [CU Utilization %] / 100

Budget Variance % =
DIVIDE([Cost per Workspace (INR)] - AVERAGE(FactCostBudget[BudgetINR]),
       AVERAGE(FactCostBudget[BudgetINR])) * 100
```

> [!TIP]
> Add calculated column `UtilizationBand = IF([CU Utilization %] > 85, "Overused", IF([CU Utilization %] < 50, "Underused", "Optimal"))`.

---

## **4️⃣ Page Layout Overview**

| Page                       | Purpose                            |
| -------------------------- | ---------------------------------- |
| **1. Capacity Overview**   | Real-time CU and cost trend        |
| **2. Workspace Breakdown** | Cost attribution by workspace      |
| **3. Budget & Variance**   | Compare actual vs budget           |
| **4. Job Performance**     | Analyze long-running workloads     |
| **5. Forecast & Alerts**   | Predictive and compliance insights |

---

## **5️⃣ Page 1: Capacity Overview**

**Layout: 3 Rows x 3 Columns**

| Visual                         | Type       | Fields                                                             | Notes              |
| ------------------------------ | ---------- | ------------------------------------------------------------------ | ------------------ |
| **Total CU Utilization**       | Gauge      | Value: [CU Utilization %]                                          | Target: 80%        |
| **Idle Hours (Card)**          | Card       | Value: [Idle Hours]                                                | Format: 0 decimals |
| **Total Monthly Cost (Card)**  | Card       | [Cost per Workspace (INR)]                                         | Currency INR       |
| **CU Utilization Trend**       | Line Chart | X: DimDate[Date], Y: [CU Utilization %]                            | 7-day view         |
| **Cost Trend (₹)**             | Area Chart | X: DimDate[Date], Y: [Cost per Workspace (INR)]                    | Smoothed           |
| **Cost by Job Type**           | Bar        | Axis: FactJobExecutions[JobType], Value: SUM(Duration)             | Color-coded        |
| **Cost by Department**         | Treemap    | Group: DimWorkspace[Department], Value: [Cost per Workspace (INR)] | For chargeback     |
| **Top 5 Expensive Workspaces** | Table      | Workspace, CU%, CostINR, Efficiency Index                          | Sorted DESC        |

> [!NOTE]
> Keep header bar static with “Fabric Capacity Utilization Dashboard”.

---

## **6️⃣ Page 2: Workspace Breakdown**

| Visual                      | Type        | Fields                                                         | Additional Setup                     |
| --------------------------- | ----------- | -------------------------------------------------------------- | ------------------------------------ |
| **Workspace Cost Table**    | Table       | Workspace, Department, CostINR, CU%, Idle Hours                | Enable conditional formatting on CU% |
| **Cost by Department**      | Donut Chart | DimWorkspace[Department], [Cost per Workspace (INR)]           | Legend right                         |
| **Cost Trend by Workspace** | Line Chart  | DimDate[Month], [Cost per Workspace (INR)], Workspace (legend) | Enable drilldown                     |
| **Efficiency Heatmap**      | Matrix      | Department vs Workspace                                        | Color scale: CU%                     |
| **Variance Alert Table**    | Table       | Workspace, CU Efficiency Index, Budget Variance %              | Filter: Budget Variance > 20%        |

---

## **7️⃣ Page 3: Budget & Variance**

| Visual                      | Type          | Fields                                               | Notes                        |
| --------------------------- | ------------- | ---------------------------------------------------- | ---------------------------- |
| **Budget vs Actual**        | Clustered Bar | Axis: Department, Values: BudgetINR & Actual CostINR | Overlay labels               |
| **Variance % by Workspace** | Column        | Axis: Workspace, Value: [Budget Variance %]          | Conditional color: red > 20% |
| **Monthly Cost Trend**      | Line          | DimDate[Month], SUM(CostINR)                         | Forecast on                  |
| **Department Budget KPI**   | Card          | Department, Variance %                               | Multi-row layout             |

> [!TIP]
> Add dynamic title:
> `="Fabric Cost Variance Overview for " & SELECTEDVALUE(DimDate[Month])`

---

## **8️⃣ Page 4: Job Performance**

| Visual                     | Type             | Fields                                       | Notes                 |
| -------------------------- | ---------------- | -------------------------------------------- | --------------------- |
| **Job Count by Type**      | Bar              | FactJobExecutions[JobType], COUNT(JobID)     | Color by job type     |
| **Avg Duration per Type**  | Clustered Column | JobType, AVERAGE(Duration)                   | Compare workloads     |
| **Top 10 Long Jobs**       | Table            | JobID, Duration, Workspace, Owner            | Sort by Duration DESC |
| **CU vs Duration Scatter** | Scatter Plot     | X: [Duration], Y: [UsedCU], Details: JobType | Trend line on         |
| **Job Trend Timeline**     | Line             | Date, SUM(Duration)                          | 30-day view           |

> [!CAUTION]
> Long-running Spark jobs (>60min) flagged in red.

---

## **9️⃣ Page 5: Forecast & Alerts**

| Visual                   | Type       | Fields                               | Notes                 |            |
| ------------------------ | ---------- | ------------------------------------ | --------------------- | ---------- |
| **Forecast CU Usage**    | Line Chart | Date, [CU Utilization %]             | Add forecast (7 days) |            |
| **Alert Matrix**         | Matrix     | AlertType vs Workspace               | Data: AlertCount      | Red = High |
| **Overuse Days (Card)**  | Card       | COUNT(Day where CU > 85%)            | Threshold icon        |            |
| **Idle Capacity (Card)** | Card       | [Idle Hours]                         | Badge color: yellow   |            |
| **Alert Details Table**  | Table      | AlertType, Severity, Workspace, Date | Interactive filter    |            |

> [!TIP]
> Integrate alerts table with Power Automate via “Export data when condition met”.

---

## **10️⃣ Interactivity Setup**

* **Cross-filtering:** All visuals interact; disable on high-cardinality tables.
* **Drillthrough pages:** Workspace → Job Details / Budget.
* **Bookmarks:**

  * “Overview” (default)
  * “Cost Focus” (filters budget pages)
  * “Performance Mode” (isolates Job view)

> [!IMPORTANT]
> Enable “Show data in tooltip” for CU % and cost visuals to expose underlying details.

---

## **11️⃣ Design and Branding**

| Element         | Style                                              |
| --------------- | -------------------------------------------------- |
| Background      | Light gray (#F5F6F8)                               |
| Header font     | Segoe UI Bold, 16pt                                |
| Body font       | Segoe UI Regular, 11pt                             |
| Accent color    | Fabric Blue (#0078D4)                              |
| KPI color codes | Green <80%, Amber 80–90%, Red >90%                 |
| Company logo    | Top-left fixed                                     |
| Footer          | “Powered by Microsoft Fabric Governance Framework” |

---

## **12️⃣ Performance Settings**

* **Storage Mode:** Dual (Direct Lake + Import for historicals)
* **Aggregations:** Pre-aggregate by `WorkspaceID + DateKey`.
* **Enable Query Caching:** ON
* **Row limit for visuals:** 10k for tables, 2k for matrix.
* **Refresh Order:** FactCapacityUsage → DimWorkspace → FactJobExecutions → FactCostBudget

---

## **13️⃣ Deliverable Output**

✅ PBIX template with linked datasets
✅ DAX measure table prebuilt
✅ Page-level design grid (1600×900 layout)
✅ Drillthrough navigation setup
✅ Power Automate alert connector preconfigured

---

Would you like me to create the **Power BI JSON Theme** file next — matching the Fabric blue-gray palette, KPI color logic, and card styling for this dashboard?
