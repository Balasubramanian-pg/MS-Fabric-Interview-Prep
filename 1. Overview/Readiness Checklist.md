Alright — here’s a tight, field-tested **Microsoft Fabric Implementation Checklist for Consultants**, built for project readiness and enterprise rollout planning.
It’s structured as a single Markdown guide you can drop into Notion, Confluence, or your consulting deck.

---

# **Microsoft Fabric Implementation Checklist for Consultants**

## **1️⃣ Pre-Implementation Readiness**

### **Governance & Licensing**

* [ ] Confirm Fabric capacity license (F64 or higher recommended for enterprise).
* [ ] Define admin roles — **Capacity Admin**, **Workspace Admin**, **Data Steward**, **Security Officer**.
* [ ] Validate Azure AD tenant integration for identity and permissions.
* [ ] Map Fabric workspaces to business units (Finance, Ops, HR, etc.).
* [ ] Enable **Microsoft Purview** integration for lineage and metadata.

> [!IMPORTANT]
> Governance setup **before ingestion** prevents downstream chaos in ownership and RLS inheritance.

---

## **2️⃣ Workspace Setup**

### **Structure**

* [ ] Create separate Fabric **workspaces per domain** (e.g., “Retail Sales”, “Inventory”, “Finance”).
* [ ] Configure **default OneLake folder** for each workspace.
* [ ] Set consistent naming: `FC_[Domain]_[Region]_[Environment]`.
* [ ] Enable **Dev → UAT → Prod** pipeline via deployment stages.
* [ ] Turn on GitHub / Azure DevOps source control integration.

> [!TIP]
> Use one capacity per environment (e.g., F32 for Dev, F64 for Prod) to isolate workloads.

---

## **3️⃣ Security & Access Control**

### **Role-Based Access**

* [ ] Define RLS (Row-Level Security) and OLS (Object-Level Security) in the semantic model.
* [ ] Apply **workspace-level permissions** based on least privilege principle.
* [ ] Set up **Service Principals** for automation and CI/CD agents.
* [ ] Enforce MFA and conditional access policies.
* [ ] Audit via **Purview + Defender for Cloud** integration.

```sql
CREATE SECURITY POLICY RegionRLS
ADD FILTER PREDICATE fn_securitypredicate(@Region)
ON dbo.SalesSummary;
```

> [!CAUTION]
> Don’t rely solely on Power BI RLS — Fabric-wide roles are more reliable for large deployments.

---

## **4️⃣ Data Architecture Planning**

### **Design Principles**

* [ ] Define **data domains**: Sales, Inventory, Customer, HR, Finance.
* [ ] Each domain maps to a **OneLake folder** and **Fabric workspace**.
* [ ] Agree on naming standards for Delta tables:

  * Example: `/OneLake/Sales/Transactions/Year=2025/Month=11/`.
* [ ] Choose **partitioning columns** early (by date, region, or store).
* [ ] Use **Delta format** everywhere — avoid mixing Parquet/CSV.

> [!NOTE]
> Consistent Delta formatting is essential for Direct Lake and Synapse interoperability.

---

## **5️⃣ Data Ingestion Strategy**

| Source Type  | Recommended Approach     | Fabric Tool                 |
| ------------ | ------------------------ | --------------------------- |
| SQL / Oracle | Incremental CDC          | Data Factory (Gen2)         |
| SAP          | OData / Table connector  | Data Factory (Gen2)         |
| API / JSON   | REST connector           | Dataflow Gen2               |
| IoT / Event  | Stream ingestion         | Synapse Real-Time Analytics |
| Flat Files   | Shortcut / Direct Upload | OneLake Explorer            |

* [ ] Define **refresh cadence** (batch vs real-time).
* [ ] Automate pipeline monitoring via alerts.
* [ ] Use **Shortcuts** for read-only external data (e.g., S3).

> [!TIP]
> Begin with 2–3 critical data sources to stabilize schema evolution before full rollout.

---

## **6️⃣ Transformation & Modeling**

### **Data Engineering**

* [ ] Build reusable **PySpark notebooks** for cleaning and joining datasets.
* [ ] Store transformation scripts in `/Engineering/Notebooks/`.
* [ ] Use **Delta checkpoints** for large dataflows.
* [ ] Leverage **Lakehouse tables** for analytics-ready data.

### **Data Modeling**

* [ ] Create **semantic models** in Power BI backed by Direct Lake.
* [ ] Apply consistent metric definitions across departments.
* [ ] Reuse models for multiple dashboards.

> [!IMPORTANT]
> Semantic model reuse = single version of truth across departments.

---

## **7️⃣ Machine Learning & AI**

* [ ] Enable Synapse Data Science in Fabric workspace.
* [ ] Register datasets with Azure ML.
* [ ] Train baseline models (e.g., demand forecast, churn).
* [ ] Store ML outputs in OneLake under `/Models/Outputs/`.
* [ ] Integrate inference results directly into Power BI.

```python
from sklearn.linear_model import LinearRegression
model.fit(X_train, y_train)
joblib.dump(model, "OneLake/Models/SalesPredictor.pkl")
```

> [!TIP]
> Co-locating models and data in OneLake simplifies re-training and CI/CD automation.

---

## **8️⃣ Real-Time & Streaming Analytics**

* [ ] Configure Event Hub → KQL Database → Power BI connection.
* [ ] Define retention policies on streaming data (7–30 days).
* [ ] Optimize KQL with pre-aggregations and summarization.
* [ ] Use **Power BI DirectQuery** for lightweight monitoring dashboards.

> [!WARNING]
> Avoid overloading KQL DB with historical queries — archive old data to OneLake regularly.

---

## **9️⃣ Power BI Integration**

* [ ] Use **Direct Lake Mode** wherever possible.
* [ ] Design dashboards aligned to semantic models.
* [ ] Publish reports to M365 Teams channels.
* [ ] Enable **data-driven alerts** for KPI thresholds.
* [ ] Use **deployment pipelines** for version-controlled publishing.

> [!NOTE]
> Direct Lake eliminates refresh schedules and enables real-time performance.

---

## **🔟 Automation and Action Layer**

* [ ] Define triggers in **Data Activator** (e.g., low inventory, sales spike).
* [ ] Connect Data Activator → Power Automate → Teams / ServiceNow.
* [ ] Store trigger definitions in `/Automation/Policies/`.
* [ ] Test notifications in sandbox before production rollout.

> [!CAUTION]
> Set threshold limits carefully — avoid notification spam in Teams.

---

## **11️⃣ Monitoring & Cost Management**

* [ ] Enable **Capacity Metrics App** to monitor CU usage.
* [ ] Track job run times and pipeline failures via **Activity Logs**.
* [ ] Archive logs to OneLake for analysis.
* [ ] Apply **auto-pause** on warehouses and notebooks during idle hours.
* [ ] Use **Fabric billing reports** to track department-level consumption.

> [!TIP]
> Review CU utilization weekly; scale up/down capacity proactively.

---

## **12️⃣ Documentation & Handover**

* [ ] Create a **Fabric Runbook** (workspace map, pipelines, access matrix).
* [ ] Document data lineage using **Purview export**.
* [ ] Maintain a **Change Log** for each workspace (version-controlled).
* [ ] Set up monthly data governance reviews.
* [ ] Train business users on Power BI self-service and Q&A.

---

## **13️⃣ Go-Live Validation Checklist**

| Validation Area | Key Checks                                          |
| --------------- | --------------------------------------------------- |
| **Data**        | Delta tables readable in OneLake, schema consistent |
| **Security**    | RLS/OLS enforced, permissions tested                |
| **Performance** | Dashboard load time < 5 seconds                     |
| **Governance**  | Purview lineage visible end-to-end                  |
| **Automation**  | Alerts firing correctly, no false triggers          |
| **User Access** | Licenses assigned, workspace permissions verified   |

> [!IMPORTANT]
> Validate governance and alert reliability before enabling executive access.

---

## **14️⃣ Post-Go-Live Maintenance**

* [ ] Review pipelines and triggers weekly.
* [ ] Conduct monthly data quality audits.
* [ ] Refresh Power BI dataset relationships if schema changes.
* [ ] Monitor ML model drift quarterly.
* [ ] Rotate service principal credentials regularly.

## **Fabric Deployment Summary**

| Phase           | Core Tools                        | Owner              | Output               |
| --------------- | --------------------------------- | ------------------ | -------------------- |
| Foundation      | Admin Portal, Purview             | Governance Team    | Fabric workspace map |
| Ingestion       | Data Factory, Real-Time Analytics | Data Engineering   | Raw Delta data       |
| Modeling        | Synapse + Power BI                | BI Team            | Semantic models      |
| AI + Automation | Synapse DS + Data Activator       | Data Science / Ops | Automated decisions  |

> [!TIP]
> Every Fabric rollout benefits from a **"3-layer responsibility model"**:
>
> 1. **Governance** (CIO/IT)
> 2. **Engineering** (Data Ops)
> 3. **Consumption** (BI / Business Units)

## **Final Consultant Checklist Summary**

✅ OneLake structure designed and secured
✅ Workspaces and capacities assigned
✅ Governance and RLS configured
✅ Data pipelines and transformations tested
✅ Semantic models published
✅ Power BI dashboards deployed
✅ Data Activator workflows validated
✅ Purview lineage complete
✅ Cost and performance baselines captured
