Alright — here’s the full **Microsoft Fabric Governance + Cost Dashboard Developer Handbook (Technical Edition)** in Markdown format.
It’s written so your BI team can reproduce and maintain the entire solution from scratch.

---

# **Microsoft Fabric Governance + Cost Dashboard Developer Handbook**

---

## **1️⃣ Overview**

This handbook provides a **step-by-step implementation guide** for building and maintaining the **Fabric Governance & Cost Optimization Dashboard** using **Power BI** and **Power Automate**.
It covers data model setup, DAX development, page layouts, alert automation, deployment, and governance controls.

> [!NOTE]
> The handbook assumes that the developer already has access to Fabric capacities, Power BI Premium workspace, and the Fabric Capacity Metrics App dataset.

---

## **2️⃣ Architecture Summary**

### **Components Involved**

| Layer             | Component                  | Purpose                                                   |
| ----------------- | -------------------------- | --------------------------------------------------------- |
| **Data Source**   | Fabric Metrics App         | Source for CU usage, job executions, and capacity data    |
| **Storage**       | OneLake / Fabric Lakehouse | Centralized storage for historical usage and cost mapping |
| **Modeling**      | Power BI Dataset           | Semantic layer containing measures and relationships      |
| **Visualization** | Power BI Dashboard         | Cost tracking and efficiency reporting                    |
| **Automation**    | Power Automate Flows       | Triggers alerts, sends Teams/email notifications          |
| **Security**      | RLS in Power BI            | Department- and user-level access control                 |

> [!IMPORTANT]
> The dashboard should run on **Direct Lake Mode** to eliminate data duplication and maintain real-time performance.

---

## **3️⃣ Data Preparation**

### **Step 1: Source Connections**

1. Connect Power BI Desktop to **Fabric Capacity Metrics App**.
2. Import the following tables:

   * `CapacityUsage`
   * `WorkspaceUsage`
   * `JobExecutions`
   * `PipelineRuns`
3. Connect to **OneLake** for the following reference tables:

   * `CostCenterMapping`
   * `WorkspaceTagging`
   * `BudgetTable`

### **Step 2: Relationship Model**

| From                           | To                              | Relationship | Type   |
| ------------------------------ | ------------------------------- | ------------ | ------ |
| `CapacityUsage[WorkspaceID]`   | `WorkspaceTagging[WorkspaceID]` | 1 → Many     | Active |
| `CapacityUsage[Date]`          | `DimDate[Date]`                 | 1 → Many     | Active |
| `WorkspaceTagging[CostCenter]` | `CostCenterMapping[CostCenter]` | 1 → Many     | Active |

### **Step 3: Data Cleaning Rules**

* Convert all timestamps to local timezone (IST).
* Ensure `WorkspaceID` is consistent across all tables.
* Handle null costs as zero.
* Create a derived column for *Environment* (Dev/UAT/Prod).

> [!TIP]
> Schedule OneLake reference tables to refresh weekly; Fabric metrics refresh daily.

---

## **4️⃣ DAX Measure Library**

### **Create a new table:**

`_Fabric_Metrics`

Include the following measures:

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

**Optional Helper Measures:**

```DAX
Underutilized Capacity Count =
COUNTROWS(FILTER(FactCapacityUsage, [CU Utilization %] < 40))

Overused Capacity Count =
COUNTROWS(FILTER(FactCapacityUsage, [CU Utilization %] > 85))
```

---

## **5️⃣ Power BI Dashboard Build Steps**

### **Page 1: Capacity Overview**

1. Add **KPI Cards** for:

   * CU Utilization %
   * Idle Hours
   * Monthly Cost (₹)
   * Efficiency Index
2. Add a **line chart** – CU Utilization % by Date.
3. Add an **area chart** – Cost trend by Date.
4. Add a **treemap** – Cost by Department.
5. Add a **table** – Top 10 Workspaces by Cost.

> [!TIP]
> Use the JSON theme “Fabric_Cost_Governance.json” for consistent colors and typography.

---

### **Page 2: Workspace Breakdown**

1. Create a **table** for Workspace, Department, CU%, Cost, Efficiency.
2. Add a **donut chart** – Cost by Department.
3. Add a **line chart** – Cost by Workspace (trend over time).
4. Add a **matrix** – Workspace vs Department with CU% gradient.
5. Use slicers for Department, Environment, and Date.

---

### **Page 3: Budget & Variance**

1. Add **clustered bar chart** – Budget vs Actual per department.
2. Add **column chart** – Variance % by Workspace.
3. Add **line chart** – Monthly Cost Trend.
4. Add KPI Cards – Total Variance %, Overused Workspaces.

---

### **Page 4: Job Performance**

1. Add **bar chart** – Job count by Type.
2. Add **scatter plot** – Duration vs CU Usage.
3. Add **table** – Top 10 Long-running Jobs.
4. Add **heatmap matrix** – Department vs Job Duration.

---

### **Page 5: Forecast & Alerts**

1. Line Chart – CU Utilization Forecast (7 days).
2. Card – Days > 85% CU.
3. Table – Active Alerts with Severity and Timestamp.
4. Matrix – Alerts by Department and Workspace.

---

## **6️⃣ Applying the Theme**

1. Import **Fabric_Cost_Governance.json**:
   `View > Themes > Browse for themes > Import theme`
2. Verify the following:

   * Primary color: `#0078D4`
   * Background: `#F5F6F8`
   * Font: Segoe UI
   * KPI green/orange/red logic applied correctly

> [!CAUTION]
> If importing fails, reapply font and color manually for KPIs — Power BI sometimes skips JSON overrides for old visuals.

---

## **7️⃣ Power Automate Integration**

### **Use Case 1: CU Utilization Alerts**

**Trigger:** Daily refresh completion of Fabric Metrics dataset.
**Action:**

1. Add a “Refresh completed” trigger from Power BI connector.
2. Add a “Get data” step to read `CU Utilization %` from dataset.
3. Add conditional logic:

   * If >90%, send Teams message → “Capacity {{CapacityName}} exceeds safe utilization.”
   * If <40%, send email → “Underutilized Fabric capacity detected.”

**Delivery Targets:**

* Teams Channel: #fabric-governance-alerts
* Email Group: [fabric-ops@company.com](mailto:fabric-ops@company.com)

---

### **Use Case 2: Idle Capacity Reminder**

**Trigger:** Scheduled at 8 PM daily.
**Steps:**

1. Run Power BI “Query data” for `[Idle Hours]`.
2. If idle hours > 8, post message:

   * “Workspace {{Name}} has been idle for {{IdleHours}} hours. Consider pausing capacity.”

---

### **Use Case 3: Auto-Pause Approval Flow**

**Trigger:** When idle >12 hours.
**Action Chain:**

1. Create Approval Card in Teams for Fabric Admin.
2. If approved → Call HTTP action to Fabric Admin API:

   * `POST /v1/capacities/{id}/pause`
3. Send confirmation to Teams and email log.

> [!NOTE]
> The auto-pause feature requires admin rights; store API credentials securely in Azure Key Vault or environment variables.

---

## **8️⃣ Role-Based Access Control (RLS)**

| Role                | Access Scope         |
| ------------------- | -------------------- |
| **CIO / Finance**   | All capacities       |
| **Department Head** | Filter by department |
| **Fabric Admin**    | Full access          |
| **Analyst**         | Workspace-level only |

**DAX Filter Example:**

```DAX
[Department] = LOOKUPVALUE(UserDepartmentMap[Department], UserDepartmentMap[Email], USERPRINCIPALNAME())
```

Assign RLS roles via:
`Modeling > Manage Roles > Add > Define Filter > Save > Publish`.

---

## **9️⃣ Publishing and Deployment**

1. Publish report to workspace: **Fabric_Governance**.
2. Enable scheduled refresh (daily, 6 AM).
3. Assign capacity (F32 or higher recommended).
4. Share with governance and finance teams via Power BI app.
5. Add report link to **SharePoint Governance Portal**.

> [!TIP]
> Always publish from **master PBIX** stored in version-controlled folder:
> `/OneLake/Governance/Reports/Fabric_Governance.pbix`

---

## **🔟 Validation & Testing**

| Test Case               | Expected Outcome                         |
| ----------------------- | ---------------------------------------- |
| Refresh test            | Dataset refresh completes within 10 mins |
| CU Utilization accuracy | Matches Fabric Metrics App               |
| Alerts trigger          | Teams messages sent correctly            |
| Budget variance         | Aligns with finance records              |
| RLS filter              | Correct user visibility verified         |

---

## **11️⃣ Maintenance SOP**

### **Frequency**

| Task                        | Frequency | Owner   |
| --------------------------- | --------- | ------- |
| Refresh validation          | Daily     | Admin   |
| Capacity utilization audit  | Weekly    | Ops     |
| Cost allocation update      | Monthly   | Finance |
| Theme review                | Quarterly | BI Lead |
| Power Automate logs cleanup | Monthly   | Admin   |

### **Backup Policy**

* Store .pbix and Power Automate flows in **Git / OneDrive**.
* Maintain version history: `v1.0`, `v1.1`, etc.
* Backup after every major DAX or layout change.

---

## **12️⃣ Troubleshooting Guide**

| Issue                     | Possible Cause             | Fix                      |
| ------------------------- | -------------------------- | ------------------------ |
| Dataset refresh fails     | API timeout                | Use incremental refresh  |
| CU Utilization showing 0% | Missing data for weekend   | Filter nulls in DAX      |
| Alerts not firing         | Incorrect condition syntax | Use preview run to debug |
| Slow visuals              | Too many columns imported  | Optimize model size      |
| RLS misfire               | USERPRINCIPALNAME mismatch | Check Azure AD emails    |

> [!TIP]
> Enable Performance Analyzer in Power BI Desktop to check slow queries.

---

## **13️⃣ Continuous Improvement**

* Conduct quarterly reviews with CFO/COO for KPI relevance.
* Add new automation flows (auto-scale, anomaly detection).
* Periodically benchmark CU cost vs Azure Synapse workloads.
* Integrate cost data into company-wide financial dashboards.

---

## **14️⃣ Summary**

A successful Fabric Governance + Cost dashboard ensures:
✅ Transparent cost accountability
✅ Real-time CU monitoring
✅ Automated efficiency alerts
✅ Unified view across Fabric capacities

> [!FINAL NOTE]
> Store this handbook under:
> `/Governance/Documentation/Fabric_Governance_Developer_Handbook_v1.0.md`
> Future updates should include screenshots and flow diagrams once the UI is finalized.

---

Would you like me to now add **a separate section on "Automated Scaling via Power Automate + Fabric REST API"** (still instruction-based, but focused only on dynamic up/down scaling of capacity)?
