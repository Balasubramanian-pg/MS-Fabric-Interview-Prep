# **Fabric Cost Governance Dashboard Blueprint (Power BI Design)**

A practical companion to the optimization guide — this dashboard visualizes **cost, CU efficiency, idle capacity, and workload attribution**.

---

## **1️⃣ Purpose**

* Track CU usage and idle time by workspace and department.
* Attribute costs to cost centers or teams.
* Detect underutilized or over-scaled capacities.
* Monitor month-over-month cost trends and scaling events.

> [!NOTE]
> The dashboard ties directly into your **Fabric Metrics App dataset** and any **custom cost tables** you maintain in OneLake.

---

## **2️⃣ Data Model**

| Table               | Description                      | Key Columns                                           |
| ------------------- | -------------------------------- | ----------------------------------------------------- |
| `FactCapacityUsage` | Hourly CU usage and cost         | CapacityID, WorkspaceID, UsedCU, AllocatedCU, CostINR |
| `FactJobExecutions` | Pipeline and Spark job durations | JobID, Type, Duration, WorkspaceID                    |
| `DimWorkspace`      | Workspace metadata               | WorkspaceID, Department, Environment, CostCenter      |
| `DimDate`           | Calendar table                   | DateKey, Month, Year                                  |
| `DimCostCenter`     | Finance mapping                  | CostCenter, Department, BudgetINR                     |

Relationships:
`DimWorkspace` → `FactCapacityUsage`
`DimWorkspace` → `FactJobExecutions`
`DimDate` → `FactCapacityUsage`

---

## **3️⃣ DAX Measure Library**

```DAX
CU Utilization % =
DIVIDE(SUM(FactCapacityUsage[UsedCU]),
       SUM(FactCapacityUsage[AllocatedCU])) * 100

Idle Hours =
SUMX(FactCapacityUsage,
     IF(FactCapacityUsage[UsedCU] = 0, 1, 0))

Cost per Workspace (INR) =
SUM(FactCapacityUsage[CostINR])

CU Efficiency Index =
(100 - [Idle Hours]) * [CU Utilization %] / 100

Budget Variance % =
DIVIDE([Cost per Workspace (INR)] - AVERAGE(DimCostCenter[BudgetINR]),
       AVERAGE(DimCostCenter[BudgetINR])) * 100
```

> [!TIP]
> Add a calculated column for **Cost Category** =
> `IF([CU Utilization %] < 40, "Underused", IF([CU Utilization %] > 85, "Overused", "Optimal"))`

---

## **4️⃣ Dashboard Pages**

### **Page 1: Capacity Overview**

* **Line Chart:** CU utilization % by hour/day.
* **Gauge:** Current utilization vs target (80%).
* **Treemap:** Cost per workspace.
* **Card:** Idle hours this month.
* **Bar Chart:** Cost per job type (Spark, Pipeline, BI, KQL).

### **Page 2: Workspace Cost Breakdown**

* **Table:** Workspace, Owner, CostCenter, CU%, CostINR, Efficiency Index.
* **Donut:** Cost share by department.
* **Trend Chart:** Cost per workspace over last 30 days.

### **Page 3: Budget & Variance**

* **Clustered Bar:** Actual vs Budget per cost center.
* **Card:** Total variance %.
* **Heatmap:** Variance by workspace.
* **Slicer:** Department, Environment.

### **Page 4: Job-Level Insights**

* **Bar Chart:** Avg job duration per type.
* **Scatter:** CU usage vs duration.
* **Table:** Top 10 long-running jobs.

### **Page 5: Forecast & Alerts**

* **Forecast Line:** Predicted CU usage (7-day).
* **KPI Card:** Days above 85% CU.
* **Matrix:** Alert count by type (Overuse, Idle, Budget Breach).

---

## **5️⃣ Visual Hierarchy Example**

```text
[Header]  Microsoft Fabric Cost Governance Dashboard
─────────────────────────────────────────────────────
 Top Row → KPIs (CU Utilization %, Idle Hours, Total Cost, Variance)
 Mid Row → Trends (Line charts for usage & cost)
 Bottom  → Details (Workspace cost table, Job breakdown)
 Sidebar → Filters (Date, Department, Environment)
```

> [!NOTE]
> Keep visuals to 12–15 max per page; Direct Lake datasets remain responsive under ~50M rows.

---

## **6️⃣ Alerts & Automation**

* Power Automate trigger on `CU Utilization % > 90` or `Idle Hours > 10`.
* Sends Teams alert or email summary:
  *“Workspace Finance_Prod averaged 93% CU for last 4 hours. Consider scaling up.”*
* Monthly cost report auto-exported to SharePoint.

---

## **7️⃣ RLS Roles**

| Role            | Filter                                             |
| --------------- | -------------------------------------------------- |
| CIO             | Full access                                        |
| Department Head | `[DimWorkspace][Department] = USERPRINCIPALNAME()` |
| Finance Analyst | `[DimCostCenter][CostCenter] IN {"FIN","OPS"}`     |
| Admin           | All capacities                                     |

---

## **8️⃣ Key Insights Enabled**

✅ Identify idle or over-scaled capacities
✅ Attribute cost to departments for chargeback
✅ Predict future CU consumption
✅ Detect expensive workloads (Spark/SQL heavy)
✅ Align usage with budget forecasts

> [!IMPORTANT]
> Mature Fabric teams save **20–30% in monthly compute cost** by continuously tuning based on this dashboard.

---

Would you like me to expand this into a **ready-to-build Power BI spec sheet** next — listing every visual, data binding, and layout grid (for developers to replicate 1:1 in Power BI Desktop)?
