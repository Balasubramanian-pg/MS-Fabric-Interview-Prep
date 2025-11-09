# **Microsoft Fabric Governance – Audit Simulation Pack**

*Mock Audit Questions, Expected Evidence, and Compliance Check Templates*

---

## **1️⃣ Purpose**

This Audit Simulation Pack helps governance, data, and BI teams prepare for internal or external audits on **Microsoft Fabric** deployments.
It replicates **real audit questioning** across **security, compliance, cost control, governance, and data integrity**, providing expected responses and sample artifacts for readiness assessments.

> [!NOTE]
> This guide is intended for **self-assessment** and **pre-audit preparedness**, not formal certification.

---

## **2️⃣ Audit Scope**

| Audit Area                  | Description                              | Key Focus           |
| --------------------------- | ---------------------------------------- | ------------------- |
| **Access & Security**       | User access, MFA, RLS/OLS                | Role integrity      |
| **Data Governance**         | Data lineage, cataloging, classification | Traceability        |
| **Cost & Capacity**         | Capacity usage, CU governance, alerts    | Financial control   |
| **Operational Governance**  | Pipelines, notebooks, orchestration logs | Process control     |
| **Data Quality & Accuracy** | ETL validation, reconciliation           | Accuracy assurance  |
| **Business Continuity**     | Backup, DR, retention                    | Recovery capability |

---

## **3️⃣ Audit Simulation Structure**

Each section contains:

* **Mock Audit Questions** – typical queries from IT, Internal Audit, or Compliance.
* **Expected Evidence** – what artifacts or screenshots to provide.
* **Compliance Check Template** – table format to document control effectiveness.

---

## **4️⃣ Access & Security**

### **Mock Audit Questions**

1. How is access to Fabric capacities controlled and reviewed?
2. Do all users have MFA enabled via Entra ID?
3. What are the Fabric roles (Admin, Member, Viewer) and who holds them?
4. How is RLS (Row-Level Security) implemented in Power BI datasets?
5. How do you manage temporary user access or project-based permissions?
6. Are audit logs enabled for Power BI and Fabric usage?

### **Expected Evidence**

* Access matrix exported from **Fabric Admin portal**.
* Entra ID access review screenshots.
* RLS configuration screenshots in Power BI dataset.
* Periodic access review log (Excel/SharePoint).
* Power BI audit log exports showing user actions.

### **Compliance Check Template**

| Control ID | Control Description        | Evidence              | Owner           | Frequency | Status     |
| ---------- | -------------------------- | --------------------- | --------------- | --------- | ---------- |
| SEC-01     | MFA enforced for all users | Entra screenshot      | IT Admin        | Quarterly | ✅          |
| SEC-02     | Access review completed    | Access log Excel      | Governance Lead | Monthly   | ✅          |
| SEC-03     | RLS implemented            | Power BI file snippet | BI Lead         | Ongoing   | ⚠️ Partial |

---

## **5️⃣ Data Governance**

### **Mock Audit Questions**

1. Is all data in OneLake classified with metadata tags (PII, Confidential, etc.)?
2. How is data lineage captured across Fabric workloads?
3. What is the process for onboarding new data sources?
4. Are shortcuts and external connections reviewed for compliance?
5. How do you prevent shadow data copies?

### **Expected Evidence**

* OneLake domain classification screenshot.
* Purview/OneLake lineage visualization.
* SOP document: “Data Onboarding & Classification Workflow.”
* Shortcut governance log showing data origin and owner.

### **Compliance Check Template**

| Control ID | Control                       | Evidence            | Owner           | Frequency | Status         |
| ---------- | ----------------------------- | ------------------- | --------------- | --------- | -------------- |
| GOV-01     | Data classification applied   | OneLake metadata    | Data Steward    | Monthly   | ✅              |
| GOV-02     | Lineage mapping maintained    | Purview map         | Data Governance | Quarterly | ✅              |
| GOV-03     | Shortcut approvals documented | SharePoint register | BI Lead         | Ongoing   | ⚠️ In Progress |

---

## **6️⃣ Cost & Capacity Management**

### **Mock Audit Questions**

1. Who owns Fabric capacity cost governance?
2. Are CU utilization thresholds defined?
3. Is auto-scaling or alerting enabled for cost spikes?
4. How are workloads distributed across capacities?
5. What reports track cost trends and idle hours?

### **Expected Evidence**

* Power BI Cost Dashboard (Fabric Metrics App).
* Power Automate alert flow screenshots.
* Capacity utilization report for last 90 days.
* Budget vs Actual cost Excel sheet.

### **Compliance Check Template**

| Control ID | Control                       | Evidence          | Owner        | Frequency | Status           |
| ---------- | ----------------------------- | ----------------- | ------------ | --------- | ---------------- |
| COST-01    | CU utilization monitored      | Metrics dashboard | Fabric Admin | Daily     | ✅                |
| COST-02    | Threshold-based alerts active | Flow export       | IT Ops       | Weekly    | ✅                |
| COST-03    | Idle capacity optimized       | Auto-pause config | BI Ops       | Monthly   | ⚠️ Review Needed |

---

## **7️⃣ Operational Governance**

### **Mock Audit Questions**

1. How are pipeline failures tracked and escalated?
2. Are notebooks version-controlled?
3. How do you document pipeline changes or schema updates?
4. Is there an audit trail for Fabric workspace modifications?
5. What is your backup policy for workspaces?

### **Expected Evidence**

* Power Automate failure alert flow screenshot.
* Git/DevOps repo for notebooks and PBIX versioning.
* Fabric Change Log Excel or SharePoint form.
* Admin activity log exports.
* Workspace export schedule (JSON backups).

### **Compliance Check Template**

| Control ID | Control                  | Evidence           | Owner         | Frequency | Status     |
| ---------- | ------------------------ | ------------------ | ------------- | --------- | ---------- |
| OPS-01     | Failure alerting         | Power Automate log | Data Ops      | Daily     | ✅          |
| OPS-02     | Notebook version control | DevOps repo        | Data Eng Lead | Ongoing   | ✅          |
| OPS-03     | Workspace change log     | SharePoint tracker | Admin         | Monthly   | ⚠️ Partial |

---

## **8️⃣ Data Quality & Reconciliation**

### **Mock Audit Questions**

1. How do you ensure data completeness and accuracy after ETL?
2. What are your validation checks before data loads to OneLake?
3. Are reconciliation reports generated and reviewed?
4. How are errors tracked, corrected, and logged?

### **Expected Evidence**

* ETL validation report (row counts, checksum).
* Power BI “Data Quality Summary” dashboard.
* Reconciliation Excel sheet (source vs destination).
* Power Automate flow for anomaly alerts.

### **Compliance Check Template**

| Control ID | Control                      | Evidence          | Owner        | Frequency | Status           |
| ---------- | ---------------------------- | ----------------- | ------------ | --------- | ---------------- |
| DQ-01      | ETL validation script active | Power BI log      | Data Eng     | Daily     | ✅                |
| DQ-02      | Reconciliation completed     | Excel tracker     | Data Quality | Weekly    | ✅                |
| DQ-03      | Error logs maintained        | OneLake log table | BI Ops       | Monthly   | ⚠️ Review Needed |

---

## **9️⃣ Business Continuity & Retention**

### **Mock Audit Questions**

1. What is your RPO/RTO objective for Fabric datasets?
2. Are backup exports scheduled to OneLake or external storage?
3. How are critical datasets versioned and restored?
4. Are archived datasets encrypted and retained per policy?

### **Expected Evidence**

* Fabric backup flow configuration (Power Automate).
* Dataset retention matrix.
* Sample restore test report.
* Encryption and retention SOP document.

### **Compliance Check Template**

| Control ID | Control                       | Evidence    | Owner      | Frequency | Status            |
| ---------- | ----------------------------- | ----------- | ---------- | --------- | ----------------- |
| BC-01      | Backup policy implemented     | Flow export | IT Ops     | Weekly    | ✅                 |
| BC-02      | Restore test performed        | Test log    | BI Lead    | Quarterly | ✅                 |
| BC-03      | Retention compliance verified | Policy doc  | Governance | Annual    | ⚠️ Pending Review |

---

## **🔟 Compliance Maturity Rating**

| Category               | Control Count | Fully Compliant | Partial | Not Compliant | Score (%)          |
| ---------------------- | ------------- | --------------- | ------- | ------------- | ------------------ |
| Access & Security      | 6             | 5               | 1       | 0             | 92                 |
| Data Governance        | 5             | 4               | 1       | 0             | 88                 |
| Cost & Capacity        | 5             | 3               | 2       | 0             | 80                 |
| Operational Governance | 5             | 4               | 1       | 0             | 88                 |
| Data Quality           | 4             | 3               | 1       | 0             | 85                 |
| Continuity             | 3             | 2               | 1       | 0             | 83                 |
| **Overall**            | 28            | 21              | 7       | 0             | **86% Compliance** |

> [!TIP]
> Treat anything below **85%** as a focus area before the next audit window.

---

## **11️⃣ Audit Simulation Execution Plan**

| Phase | Objective                 | Output                       | Owner             | Duration |
| ----- | ------------------------- | ---------------------------- | ----------------- | -------- |
| 1     | Run mock audit interviews | Question response logs       | Governance Lead   | 3 days   |
| 2     | Collect evidence          | Screenshots, exports         | Control Owners    | 5 days   |
| 3     | Verify compliance score   | Audit dashboard updated      | PMO               | 2 days   |
| 4     | Identify remediation      | Action plan Excel            | Governance Team   | 3 days   |
| 5     | Conduct re-audit          | Post-correction verification | External Reviewer | 1 week   |

---

## **12️⃣ Continuous Audit Readiness Dashboard**

* **Tool:** Power BI Dashboard
* **Data Source:** SharePoint Control Tracker + OneLake Evidence Logs
* **Metrics:**

  * % Controls Fully Compliant
  * # Pending Evidence Uploads
  * Average Control Aging (days since last review)
  * Upcoming Audit Calendar

> [!IMPORTANT]
> Keep the dashboard **auto-refreshed** weekly using Power Automate and Power BI scheduled refresh.

---

## **13️⃣ Sample Audit Interview Script**

**Auditor:** “Show me how you ensure Fabric capacity costs don’t exceed monthly thresholds.”
**Expected Response:** “We have a Power Automate alert flow connected to the Fabric Metrics dataset. When CU utilization exceeds 85% for more than 30 minutes, a Teams alert is sent to the Fabric Admin Group for scaling review.”
**Evidence to Provide:** Flow screenshot, alert log from Teams, and 90-day trend graph.

---

## **14️⃣ Evidence Repository Structure**

```
/Audit/
├── Evidence/
│   ├── AccessSecurity/
│   ├── DataGovernance/
│   ├── CostManagement/
│   ├── Operations/
│   ├── DataQuality/
│   └── Continuity/
├── Checklists/
│   ├── ControlTracker.xlsx
│   ├── RiskRegister.xlsx
│   └── RemediationPlan.xlsx
├── Reports/
│   ├── AuditSummary.pbix
│   ├── ComplianceScorecards.pdf
│   └── WeeklyReadinessDashboard.pbix
└── SOPs/
    ├── DataAccessPolicy.docx
    ├── BackupRetentionSOP.docx
    └── CostGovernancePolicy.docx
```

---

## **15️⃣ Remediation Tracker Template**

| Issue ID | Area       | Description                | Root Cause               | Owner        | Target Date | Status         | Evidence Link |
| -------- | ---------- | -------------------------- | ------------------------ | ------------ | ----------- | -------------- | ------------- |
| 001      | Access     | Missing RLS on new dataset | Oversight in deployment  | BI Dev       | 15-Nov      | 🟡 In Progress | [link]        |
| 002      | Cost       | Idle capacity not paused   | Alert flow misconfigured | Fabric Admin | 12-Nov      | 🟢 Closed      | [link]        |
| 003      | Governance | Shortcut approval pending  | Missing SOP mapping      | Data Steward | 30-Nov      | 🔴 Open        | [link]        |

---

## **16️⃣ Final Audit Readiness Checklist**

| Step | Task                         | Owner           | Status     |
| ---- | ---------------------------- | --------------- | ---------- |
| 1    | Update control tracker       | Governance Lead | ✅          |
| 2    | Upload all latest evidence   | Control Owners  | ✅          |
| 3    | Refresh audit dashboard      | BI Admin        | ✅          |
| 4    | Validate all SOPs up to date | Governance PMO  | ⚠️ Pending |
| 5    | Conduct dry-run interview    | Fabric Admin    | ✅          |
| 6    | Submit pre-audit summary     | BI Lead         | ✅          |

---

## **17️⃣ Key Takeaways**

✅ Keep all Fabric audit evidence centralized (OneLake or SharePoint).
✅ Automate control status updates with Power Automate.
✅ Simulate interviews quarterly to maintain readiness.
✅ Use Power BI to visualize control maturity and risk.
✅ Align audit scope with ISO 27001, SOC 2, and internal ITGC frameworks.

> [!FINAL NOTE]
> File this as:
> `/Governance/Audit/Fabric_Audit_Simulation_Pack_v1.0.md`
> and update quarterly with refreshed control scores and new evidence samples.

---

Would you like me to create a **“Fabric Audit Dashboard Specification”** next — describing how to visualize these compliance scores, evidence uploads, and risk statuses in Power BI?
