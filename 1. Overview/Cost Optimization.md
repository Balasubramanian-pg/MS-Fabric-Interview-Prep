# **Microsoft Fabric Cost Optimization Guide**

*(Comprehensive Consultant Reference – CU Usage, Pricing, and Capacity Tuning)*

---

## **1️⃣ Overview**

Microsoft Fabric operates on a **Capacity Unit (CU)-based pricing model**, not per-user or per-workspace.
Each workload (Power BI, Synapse, Data Factory, Real-Time Analytics, etc.) draws from the same pool of CUs assigned to a **Fabric capacity**.

### **Objective**

* Optimize capacity usage across all Fabric workloads.
* Reduce idle or underutilized compute.
* Achieve predictable cost-to-performance ratios per business unit.
* Enable proactive scaling policies.

> [!NOTE]
> Cost optimization in Fabric = *balancing capacity units (compute) + workload efficiency (jobs, refreshes, queries).*

---

## **2️⃣ Fabric Pricing Model Explained**

### **A. Capacity Units (CU)**

* The **base unit of compute and memory** in Fabric.
* One CU = a proportional share of compute across Fabric services.
* Each SKU (F2, F4, F8, F16, F32, F64, F128, F256) defines how many CUs are provisioned.

| Capacity SKU    | CUs     | Recommended Use Case                             |
| --------------- | ------- | ------------------------------------------------ |
| **F2 / F4**     | 2–4     | Dev, testing, small projects                     |
| **F8 / F16**    | 8–16    | Mid-scale departmental workloads                 |
| **F32 / F64**   | 32–64   | Enterprise shared capacities                     |
| **F128 / F256** | 128–256 | Large multi-tenant or high concurrency workloads |

### **B. Billing Model**

* Charged **per hour per CU** (consumed or reserved).
* Available via **Fabric Capacity (Premium Gen2)** in Power BI Admin Portal.
* Costs accrue whether capacity is used or idle, unless **paused**.

### **C. Cost Drivers**

* Notebook executions (Spark jobs)
* SQL Warehouse queries
* Power BI dataset refreshes / Direct Lake queries
* Dataflow and pipeline activities
* Real-Time KQL queries
* Background jobs (Data Activator triggers, semantic model refreshes)

---

## **3️⃣ Cost Attribution Framework**

### **Tagging Model**

* Assign **Department / Domain / Environment** tags to each workspace.
* Store in Admin Portal or custom metadata table in OneLake.

| Workspace             | Capacity | Domain  | Department | Owner    | Cost Center |
| --------------------- | -------- | ------- | ---------- | -------- | ----------- |
| Sales_Prod            | F64      | Sales   | Marketing  | Namritha | CC-001      |
| Finance_Dev           | F4       | Finance | CFO Office | Rajesh   | CC-002      |
| SupplyChain_Analytics | F32      | Ops     | SCM        | Deepak   | CC-004      |

> [!TIP]
> Tags allow CU utilization to be mapped to departments for internal cost allocation.

---

## **4️⃣ CU Usage Analysis (Fabric Metrics)**

### **Data Source**

Use the **Fabric Capacity Metrics App** → Dataset: *Capacity Utilization*.
Available via Power BI or Admin API.

**Key Tables:**

* `CapacityUsage`
* `WorkspaceUsage`
* `JobExecutions`
* `PipelineRuns`
* `QueryPerformance`

### **Key Metrics**

| Metric                 | Description                     | Formula                        |
| ---------------------- | ------------------------------- | ------------------------------ |
| **CU Utilization %**   | Actual vs allocated compute     | `(UsedCU / AllocatedCU) * 100` |
| **CU Idle %**          | Time capacity was not used      | `100 - CU Utilization %`       |
| **CU Peak Usage**      | Maximum CU used in time period  | `MAX(UsedCU)`                  |
| **Cost per Workspace** | CU consumption × hourly CU cost | `UsedCU × CU_Rate`             |
| **Job Duration**       | Runtime of Spark or SQL jobs    | `EndTime - StartTime`          |

---

## **5️⃣ Identifying Cost Hotspots**

### **High-Cost Activities**

1. **Long-running Spark sessions** left open by engineers.
2. **Unoptimized Power BI semantic models** (large DAX cache, excessive relationships).
3. **Data Factory pipelines** with redundant transformations.
4. **High concurrency Direct Lake queries** hitting same datasets.
5. **KQL queries** running continuously without retention limits.
6. **Underutilized or idle capacities** (Dev environments left active).

> [!WARNING]
> Fabric continues billing for **active but idle capacities** — ensure they’re paused when not in use.

---

## **6️⃣ Performance vs Cost Tradeoffs**

| Workload                     | High Cost Behavior           | Optimization Strategy                                   |
| ---------------------------- | ---------------------------- | ------------------------------------------------------- |
| **Spark (Data Engineering)** | Repeated transformations     | Cache intermediate tables; reuse Delta outputs          |
| **Synapse Warehouse**        | Heavy T-SQL joins            | Use partition pruning, reduce column sets               |
| **Power BI**                 | Large imports, auto-refresh  | Switch to Direct Lake; disable auto-refresh             |
| **Data Factory**             | Frequent triggers            | Merge flows into fewer pipelines; use conditional logic |
| **KQL Analytics**            | Persistent streaming queries | Use batching + retention policies                       |
| **Data Activator**           | Excess triggers              | Aggregate conditions, use buffer windows                |

---

## **7️⃣ Optimization Techniques**

### **A. Capacity Planning**

1. Monitor average CU utilization:

   ```DAX
   AvgCUUtilization = AVERAGE(FabricCapacity[UsedCU] / FabricCapacity[AllocatedCU])
   ```
2. If below 50% for >2 weeks → **scale down**.
3. If above 85% frequently → **scale up** or load-balance workspaces.

> [!TIP]
> Keep utilization between **60–80%** — ideal range for performance and cost balance.

---

### **B. Auto-Pause Strategy**

* Schedule **auto-pause** during non-business hours (Dev/UAT).
* Fabric pauses compute, retaining storage at no cost.
* Restart automatically via API or job scheduler.

```bash
POST https://api.fabric.microsoft.com/v1/capacities/{id}/pause
```

> [!CAUTION]
> Auto-pause impacts scheduled pipelines and refreshes — ensure dependencies are shifted or rescheduled.

---

### **C. Job Scheduling & Sequencing**

* Avoid concurrent Spark and SQL warehouse workloads in same capacity.
* Run heavy batch jobs during low concurrency windows (night / weekends).
* Use **Pipeline dependency chains** instead of multiple triggers.

**Example:**

```
Pipeline 1 → Output Delta Table → Triggers Pipeline 2
```

---

### **D. Query Optimization (Power BI + Synapse)**

* Limit columns using `SELECT` projection.
* Push transformations upstream (in Data Factory or Spark).
* Use **Aggregations** in Power BI for large tables.
* Replace **imported datasets** with **Direct Lake connections**.

> [!TIP]
> Direct Lake reduces memory overhead by 60–70% for large fact tables.

---

### **E. Cost Visibility Dashboards**

Create Power BI dashboards for cost transparency:

| Metric               | Visual     | Data Source        |
| -------------------- | ---------- | ------------------ |
| CU Utilization Trend | Line chart | Fabric Metrics App |
| Cost by Workspace    | Treemap    | CapacityUsage      |
| Cost by Job Type     | Bar chart  | JobExecutions      |
| Peak Usage by Hour   | Area chart | CapacityUsage      |
| Idle Hours           | Card       | CU Idle %          |

---

## **8️⃣ Governance & Cost Accountability**

| Role                         | Responsibility                | Tool                     |
| ---------------------------- | ----------------------------- | ------------------------ |
| **CIO / Finance Controller** | Approves capacity budgets     | Power BI cost dashboards |
| **Fabric Admin**             | Monitors CU usage             | Capacity Metrics App     |
| **Data Steward**             | Identifies underused datasets | Purview lineage          |
| **Team Leads**               | Manage workspace scheduling   | Admin Portal             |
| **Governance Lead**          | Reviews monthly CU efficiency | Governance Dashboard     |

> [!NOTE]
> Tag workspaces with cost centers to enforce **chargeback models**.

---

## **9️⃣ Automation Examples**

### **CU Alert Trigger**

Trigger Power Automate flow if utilization > 90% for 2 hours.

```json
"trigger": "CUUtilization > 90 for 2 hours",
"action": {
  "type": "TeamsMessage",
  "target": "Fabric-Admin",
  "message": "Capacity {{CapacityName}} exceeding 90% CU utilization."
}
```

### **Auto-Scale via API**

```bash
POST https://api.fabric.microsoft.com/v1/capacities/scale
{
  "capacityId": "F64-WEST-1",
  "targetSku": "F128"
}
```

---

## **🔟 Advanced Optimization Scenarios**

### **1. Dev/Test Environment Consolidation**

* Host Dev & QA in shared low-tier F4–F8 capacity.
* Enable auto-pause after 1 hour idle.

### **2. Cross-Region Workload Distribution**

* Assign region-based capacities (APAC, EMEA, AMER).
* Route Power BI queries closest to data location.

### **3. Fabric + Synapse Hybrid**

* Retain heavy data warehouse in Synapse dedicated pools.
* Use Fabric for orchestration + visualization only.

### **4. Multi-Tenant Cost Partitioning**

* Tag tenant IDs on workspaces.
* Generate cost breakdown via Power BI.

---

## **11️⃣ Monthly Cost Review Template**

| Metric                   | Actual | Target | Status | Remarks                |
| ------------------------ | ------ | ------ | ------ | ---------------------- |
| CU Utilization %         | 72%    | 80%    | ✅      | Optimal                |
| Idle CU Hours            | 8.2    | <10    | ✅      | Good balance           |
| Peak CU                  | 62     | <64    | ✅      | Stable                 |
| Cost per Workspace (INR) | ₹1.2L  | ₹1.5L  | ✅      | 20% below              |
| Auto-Pause Compliance    | 90%    | 100%   | ⚠️     | Add two dev workspaces |
| Job Failure Rate         | 3%     | <2%    | ⚠️     | Check Spark sessions   |

> [!TIP]
> Include this report in your **Governance Review Deck** for CFO visibility.

---

## **12️⃣ Optimization Maturity Ladder**

| Level                  | Description                   | Typical Org Behavior          |
| ---------------------- | ----------------------------- | ----------------------------- |
| **1 – Reactive**       | Monitor manually post-billing | No capacity alerts            |
| **2 – Basic Tracking** | Monthly CU reports            | Manual scaling                |
| **3 – Proactive**      | Scheduled monitoring          | Auto-pause in dev             |
| **4 – Predictive**     | Forecasting via dashboards    | Alerts + scaling              |
| **5 – Autonomous**     | Full automation via APIs      | Policy-driven capacity tuning |

> [!IMPORTANT]
> Level 4+ organizations integrate **Power Automate + Fabric APIs** for closed-loop cost management.

---

## **13️⃣ Summary Recommendations**

* Maintain **CU Utilization 60–80%**.
* Enforce **auto-pause policies** on non-production capacities.
* Replace **imported datasets** with **Direct Lake** for large models.
* Monitor cost KPIs weekly with dashboards.
* Establish **chargeback model** by department.
* Automate scaling and alerts using Fabric REST APIs.

> [!NOTE]
> Smart tuning saves **15–35% of Fabric costs** annually in most enterprise deployments.

---

Would you like me to follow this up with a **Fabric Cost Governance Dashboard (Power BI design + DAX + visuals)** that tracks CU usage, idle time, and cost-per-workspace KPIs visually?
