# **Microsoft Fabric Audit Dashboard Specification**

*Compliance Visualization for Governance & Control Monitoring*

---

## **1️⃣ Purpose**

The **Fabric Audit Dashboard** translates compliance data into a visual command center for governance teams.
It helps track control status, evidence uploads, remediation progress, and audit readiness scores across all Fabric domains (Security, Cost, Data, and Operations).

> [!NOTE]
> This dashboard runs on Power BI, using SharePoint or OneLake as its data source.

---

## **2️⃣ Data Model Overview**

| Table                  | Description                               | Key Columns                                           | Source                                        |
| ---------------------- | ----------------------------------------- | ----------------------------------------------------- | --------------------------------------------- |
| **ControlTracker**     | Master list of controls and audit results | ControlID, Category, Status, Owner, ReviewDate        | SharePoint list or Excel                      |
| **EvidenceRegister**   | File-level audit evidence metadata        | EvidenceID, ControlID, FilePath, UploadedBy, Verified | OneLake folder or SharePoint document library |
| **RemediationTracker** | Issues and corrective actions             | IssueID, Area, Description, Owner, TargetDate, Status | Excel / SharePoint                            |
| **AuditScores**        | Numeric scoring per audit cycle           | Category, Score, AuditCycle, Reviewer                 | OneLake CSV                                   |
| **AuditSchedule**      | Upcoming reviews and deadlines            | AuditName, Date, Owner, Status                        | SharePoint list                               |

> [!TIP]
> Use **Power Automate** to refresh these data sources weekly.

---

## **3️⃣ Relationships**

* **ControlTracker[ControlID]** ↔ **EvidenceRegister[ControlID]**
* **ControlTracker[Category]** ↔ **AuditScores[Category]**
* **RemediationTracker[Area]** ↔ **ControlTracker[Category]**

---

## **4️⃣ Dashboard Architecture**

### **Page 1 – Audit Overview**

**Purpose:** Executive snapshot of overall audit readiness.
**Visuals:**

* KPI cards:

  * Overall Compliance %
  * Open Remediation Items
  * Pending Evidence Uploads
  * Next Audit Date
* Gauge chart: Audit Compliance vs Target (Goal = 90%)
* Heatmap: Compliance % by Category
* Bar chart: Control Status Distribution (Compliant / Partial / Non-Compliant)

**DAX Example:**

```DAX
OverallCompliance% = 
DIVIDE(
    COUNTROWS(FILTER(ControlTracker, ControlTracker[Status] = "Compliant")),
    COUNTROWS(ControlTracker)
)
```

---

### **Page 2 – Category Deep Dive**

**Purpose:** Drilldown into each governance area.
**Visuals:**

* Slicer: Category (Security, Data Governance, Cost, Operations, Quality, Continuity)
* Matrix: ControlID | Control Description | Owner | Status | Last Review Date
* Conditional formatting: ✅ = Green, ⚠️ = Amber, ❌ = Red
* Line chart: Compliance % trend across last 4 audit cycles

**DAX Example:**

```DAX
CategoryCompliance% = 
CALCULATE(
    DIVIDE(
        COUNTROWS(FILTER(ControlTracker, ControlTracker[Status] = "Compliant")),
        COUNTROWS(ControlTracker)
    ),
    ALLEXCEPT(ControlTracker, ControlTracker[Category])
)
```

---

### **Page 3 – Evidence Management**

**Purpose:** Track document evidence linked to controls.
**Visuals:**

* Table: ControlID | File Name | UploadedBy | Verified | Last Modified
* Card: Verified Evidence %
* Funnel chart: Evidence verification pipeline (Uploaded → Verified → Approved)
* Bar: Missing Evidence Count by Category

**DAX Example:**

```DAX
EvidenceVerified% = 
DIVIDE(
    COUNTROWS(FILTER(EvidenceRegister, EvidenceRegister[Verified] = TRUE())),
    COUNTROWS(EvidenceRegister)
)
```

---

### **Page 4 – Remediation Tracker**

**Purpose:** Monitor corrective actions from past audits.
**Visuals:**

* Table: IssueID | Area | Owner | Target Date | Status
* Gauge: % of Issues Closed
* Clustered Bar: Open Issues by Area
* Line: Average Aging Days (In Progress Issues)

**DAX Example:**

```DAX
ClosedRemediation% = 
DIVIDE(
    COUNTROWS(FILTER(RemediationTracker, RemediationTracker[Status] = "Closed")),
    COUNTROWS(RemediationTracker)
)
```

---

### **Page 5 – Audit Calendar**

**Purpose:** Display upcoming audit events and deadlines.
**Visuals:**

* Timeline chart: AuditName vs Date
* Table: Owner | Audit | Date | Status | Notes
* Conditional color:

  * Green = Scheduled
  * Amber = Due soon (within 7 days)
  * Red = Overdue

**Power BI Field Parameters:**

```DAX
AuditDueFlag = 
SWITCH(
    TRUE(),
    DATEDIFF(TODAY(), AuditSchedule[Date], DAY) < 0, "Overdue",
    DATEDIFF(TODAY(), AuditSchedule[Date], DAY) < 7, "Due Soon",
    "Scheduled"
)
```

---

### **Page 6 – Risk & Maturity Summary**

**Purpose:** Assess governance maturity visually.
**Visuals:**

* Radar Chart: Category vs Maturity Score
* Stacked Column: Risk Severity (High / Medium / Low) by Category
* KPI Card: Average Maturity Level
* Trend line: Overall Score over time

---

## **5️⃣ Color & Theming Standards**

| Status            | Color | Hex       |
| ----------------- | ----- | --------- |
| **Compliant**     | Green | `#16A34A` |
| **Partial**       | Amber | `#FACC15` |
| **Non-Compliant** | Red   | `#DC2626` |
| **Verified**      | Blue  | `#2563EB` |
| **Pending**       | Gray  | `#9CA3AF` |

> [!TIP]
> Use a **Fabric JSON theme file** with consistent corporate colors.

---

## **6️⃣ Automation & Alerts**

### **Power Automate Flows**

| Flow Name                  | Trigger                    | Action                                      | Recipients     |
| -------------------------- | -------------------------- | ------------------------------------------- | -------------- |
| **Evidence Reminder Flow** | Every Monday               | Email users missing uploads                 | Control Owners |
| **Audit Status Alert**     | When new audit cycle added | Teams message: “Audit Cycle 2025 Initiated” | Audit Group    |
| **Remediation Escalation** | Issue status = “Overdue”   | Tag owner + supervisor                      | Governance PMO |

> [!NOTE]
> Store all flow exports in `/Automation/Flows/Audit_Dashboard_Alerts.zip`.

---

## **7️⃣ RLS (Row-Level Security) Design**

| Role                   | Access                                        |
| ---------------------- | --------------------------------------------- |
| **Governance Admin**   | Full access                                   |
| **Control Owner**      | Sees only their category or assigned controls |
| **Auditor (External)** | Read-only view of evidence and control status |
| **Executive**          | Summary pages only                            |

> [!CAUTION]
> Always test RLS filters before publishing — avoid exposing evidence file paths unintentionally.

---

## **8️⃣ Dataset Refresh & Scheduling**

| Dataset            | Source          | Refresh Frequency | Owner           |
| ------------------ | --------------- | ----------------- | --------------- |
| ControlTracker     | SharePoint List | Daily             | BI Admin        |
| EvidenceRegister   | OneLake Folder  | Hourly            | BI Admin        |
| RemediationTracker | Excel Sheet     | Weekly            | PMO             |
| AuditSchedule      | SharePoint      | Daily             | Governance Lead |

---

## **9️⃣ Key KPIs Summary**

| KPI                        | Description                        | Target | Formula                       |
| -------------------------- | ---------------------------------- | ------ | ----------------------------- |
| **Overall Compliance %**   | Ratio of compliant controls        | ≥ 90%  | `COUNT(Compliant)/COUNT(All)` |
| **Evidence Verified %**    | Verified evidence vs total uploads | ≥ 95%  | Verified/Total                |
| **Remediation Closure %**  | Closed issues vs total             | ≥ 85%  | Closed/Total                  |
| **Average Maturity Score** | Mean score across categories       | ≥ 4/5  | AVERAGE(Scores)               |
| **Audit Readiness Days**   | Days left to next audit            | ≥ 7    | DATEDIFF(TODAY(), AuditDate)  |

---

## **🔟 Governance Review Template**

### **Monthly Review Snapshot (PowerPoint Export)**

1. **Slide 1:** Audit Readiness Summary
2. **Slide 2:** Compliance Score by Category
3. **Slide 3:** Remediation Progress Heatmap
4. **Slide 4:** Evidence Upload Timeliness
5. **Slide 5:** Risk & Maturity Radar

> [!TIP]
> Schedule Power BI → PowerPoint export via **Power Automate “Export and Email Report”** connector.

---

## **11️⃣ Data Model Folder Structure**

```
/AuditDashboard/
├── DataModel/
│   ├── ControlTracker.csv
│   ├── EvidenceRegister.csv
│   ├── RemediationTracker.xlsx
│   └── AuditScores.csv
├── Theme/
│   └── Fabric_AuditDashboard_Theme.json
├── PowerBI/
│   └── Fabric_AuditDashboard.pbix
└── Automation/
    ├── EvidenceReminderFlow.zip
    ├── AuditStatusAlert.zip
    └── RemediationEscalation.zip
```

---

## **12️⃣ Deployment & Version Control**

1. Store `.pbix` in Git or SharePoint with version tags (e.g., `v1.0`, `v1.1`).
2. Document all dataset schema changes in `/Changelog.md`.
3. Assign dashboard ownership to **BI Governance Team**.
4. Maintain backup in OneLake: `/Governance/AuditDashboard_Backups/`.

---

## **13️⃣ Testing Checklist**

| Test                    | Description                          | Expected Result | Status |
| ----------------------- | ------------------------------------ | --------------- | ------ |
| Data load validation    | Dataset refresh successful           | ✅               |        |
| RLS filters             | Each role sees correct scope         | ✅               |        |
| KPI validation          | All cards display correct metrics    | ✅               |        |
| Alert triggers          | Power Automate notifications working | ✅               |        |
| Drillthrough navigation | Page transitions functional          | ✅               |        |

---

## **14️⃣ Business Impact Summary**

✅ Full transparency on control compliance.
✅ Single dashboard replacing Excel trackers.
✅ Automated alerts reduce missed evidence uploads.
✅ PowerPoint exports simplify governance reviews.
✅ Strengthens audit confidence and regulatory posture.

---

## **15️⃣ File Naming and Storage Convention**

| File Type | Naming Pattern                    | Example                             |
| --------- | --------------------------------- | ----------------------------------- |
| Dataset   | `Audit_{Source}_YYYYMMDD.csv`     | `Audit_ControlTracker_20251109.csv` |
| PBIX      | `Fabric_AuditDashboard_v{n}.pbix` | `Fabric_AuditDashboard_v1.0.pbix`   |
| Theme     | `Fabric_AuditTheme.json`          | `Fabric_AuditTheme.json`            |
| Flow      | `Audit_{FlowName}.zip`            | `Audit_EvidenceReminder.zip`        |

---

> [!FINAL NOTE]
> Store this document under:
> `/Governance/DashboardSpecs/Fabric_AuditDashboard_Spec_v1.0.md`
> It should be updated whenever a new control category, KPI, or data source is added.

---

Would you like me to follow this up with the **Power BI JSON theme file** (colors, typography, cards, and visuals for this dashboard)?
