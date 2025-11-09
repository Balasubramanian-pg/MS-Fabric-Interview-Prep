# **Fabric Audit Dashboard – UI Layout Wireframe Specification**

*A visual guide for layout, alignment, and navigation consistency*

---

## **1️⃣ Objective**

This document defines the **visual structure and layout standards** for the Microsoft Fabric Audit Dashboard.
It ensures every version or developer reproduces the same high-clarity, executive-ready design — without design drift.

> [!NOTE]
> Layout grid based on **16:9 Power BI canvas (1920×1080)** with **4-column structure** and **uniform padding (16px)**.

---

## **2️⃣ Dashboard Grid System**

| Parameter             | Value                                   |
| --------------------- | --------------------------------------- |
| Canvas Ratio          | 16:9                                    |
| Column Count          | 4                                       |
| Gutter Width          | 24px                                    |
| Section Padding       | 16px                                    |
| Header Height         | 100px                                   |
| Footer Height         | 60px                                    |
| Card Height (KPI)     | 120px                                   |
| Standard Visual Block | 400×300px                               |
| Font Scaling          | Responsive (auto-size off, manual lock) |

---

## **3️⃣ Page 1: Audit Overview (Executive Summary)**

**Purpose:** Single-glance compliance and readiness summary.

```
┌──────────────────────────────────────────────────────────────────────────┐
│                      [TITLE BAR – Fabric Audit Dashboard]                │
│   Subtitle: “Governance | Compliance | Cost | Data Integrity”            │
├──────────────────────────────────────────────────────────────────────────┤
│ [KPI Card 1] [KPI Card 2] [KPI Card 3] [KPI Card 4]                      │
│  (Overall %) (Open Issues) (Evidence Pending) (Next Audit)               │
├──────────────────────────────────────────────────────────────────────────┤
│ [Gauge: Compliance vs Target]   [Bar: Control Status by Category]        │
│ [Heatmap: Category Compliance]  [Line: Audit Score Trend 4 cycles]       │
├──────────────────────────────────────────────────────────────────────────┤
│ Footer: “Data Source: SharePoint / OneLake | Last Refreshed: [Date]”     │
└──────────────────────────────────────────────────────────────────────────┘
```

**Interactions:**

* Clicking category in heatmap → drills to “Category Deep Dive.”
* Tooltip shows compliance delta from previous cycle.

---

## **4️⃣ Page 2: Category Deep Dive**

**Purpose:** Diagnose specific governance area performance.

```
┌─────────────────────────────┬──────────────────────────────────────────┐
│  [Slicer: Category]         │  [Line Chart: Trend by Audit Cycle]     │
│  [Slicer: Owner]            │                                          │
├─────────────────────────────┼──────────────────────────────────────────┤
│ [Matrix: Control Detail Table] (ControlID | Description | Owner | ...) │
├─────────────────────────────┼──────────────────────────────────────────┤
│ [Donut Chart: Status Split] │ [Bar: Aging of Controls >30 Days]       │
└─────────────────────────────┴──────────────────────────────────────────┘
```

**Layout Tip:** Keep text columns left-aligned for readability; cap table rows to 15 for performance.

---

## **5️⃣ Page 3: Evidence Management**

**Purpose:** Track completeness of uploaded and verified evidence.

```
┌────────────────────────────────────────────────────────────────────────┐
│ [KPI Card: Verified %]   [KPI Card: Missing Evidence] [Upload Trend]   │
├────────────────────────────────────────────────────────────────────────┤
│ [Funnel Chart: Upload → Verify → Approve]                              │
│ [Table: Evidence Register (ControlID | FileName | Verified | Owner)]   │
├────────────────────────────────────────────────────────────────────────┤
│ [Bar Chart: Missing Evidence by Category]                              │
└────────────────────────────────────────────────────────────────────────┘
```

**Interactions:**

* Clicking a control ID in the table → opens document link in SharePoint.
* Hover tooltips display verification date and reviewer name.

---

## **6️⃣ Page 4: Remediation Tracker**

**Purpose:** Monitor audit issue closures and delays.

```
┌───────────────────────────────────────────────────────────────────────┐
│ [Gauge: % Issues Closed]  [KPI Card: Avg Aging Days]                  │
├───────────────────────────────────────────────────────────────────────┤
│ [Table: IssueID | Area | Owner | Target Date | Status]                │
│ [Clustered Bar: Open Issues by Area]                                 │
│ [Line: Aging Trend over Time]                                        │
└───────────────────────────────────────────────────────────────────────┘
```

**Highlight:** Use color gradient on Target Date (Red = overdue, Amber = due soon, Green = closed).

---

## **7️⃣ Page 5: Audit Calendar**

**Purpose:** Timeline of upcoming audits and readiness milestones.

```
┌──────────────────────────────────────────────────────────────────────┐
│ [Timeline Visual: AuditName vs Date (Color = Status)]                 │
│ [Card: Next Audit Days Left] [Card: Audit Count per Quarter]          │
├──────────────────────────────────────────────────────────────────────┤
│ [Table: Audit | Owner | Status | Date | Notes]                        │
└──────────────────────────────────────────────────────────────────────┘
```

**Interactions:**

* Clicking timeline bar filters table below.
* Tooltip shows responsible owner and evidence % readiness.

---

## **8️⃣ Page 6: Risk & Maturity Dashboard**

**Purpose:** Visual summary of governance risk and maturity scoring.

```
┌──────────────────────────────────────────────────────────────────────┐
│ [Radar Chart: Category vs Maturity Score]                            │
│ [Stacked Column: Risk Severity Distribution]                         │
├──────────────────────────────────────────────────────────────────────┤
│ [Table: Category | Score | Reviewer | Last Updated]                   │
│ [Line: Overall Governance Maturity Trend]                             │
└──────────────────────────────────────────────────────────────────────┘
```

**Tip:** Use shaded radar fill (blue gradient) and markers labeled by category initials.

---

## **9️⃣ Navigation Bar (Persistent Across Pages)**

**Position:** Top horizontal strip (height 50px)
**Buttons:**

* Overview | Category | Evidence | Remediation | Calendar | Risk
* Active page highlighted in **Fabric Blue (#2563EB)**

**Power BI Implementation:**
Use **buttons with page navigation actions** and maintain consistent icons:

| Icon | Label       | Function               |
| ---- | ----------- | ---------------------- |
| 🏠   | Overview    | Return to summary      |
| 📊   | Category    | Governance deep dive   |
| 📁   | Evidence    | Open evidence tracking |
| 🧾   | Remediation | Track issues           |
| 📅   | Calendar    | View schedule          |
| ⚙️   | Risk        | View maturity          |

> [!TIP]
> Use **SVG icons** for crisp rendering in both desktop and web service.

---

## **10️⃣ Visual Hierarchy & Design Rules**

* **Titles:** Tenorite 14px, Bold, Charcoal (#111827)
* **Subtitles / Section headers:** Afacad 12px, Medium, Gray (#4B5563)
* **Body Text:** Afacad 10–11px
* **Card Values:** Tenorite 22px, Primary Blue (#2563EB)
* **Margins:** 8px internal padding inside all visuals
* **Borders:** Light Gray (#E5E7EB), 1px solid
* **Background:** White (#FFFFFF) blocks on light neutral background (#F8FAFC)

> [!IMPORTANT]
> Keep no more than **8 visuals per page** to preserve performance and clarity.

---

## **11️⃣ Mobile View Adaptation**

| Page        | Layout Changes                         |
| ----------- | -------------------------------------- |
| Overview    | Stack KPI cards vertically (2 columns) |
| Category    | Collapse table under slicers           |
| Evidence    | Hide funnel, retain table              |
| Remediation | Replace bar chart with donut summary   |
| Calendar    | Simplify timeline to list view         |
| Risk        | Display radar chart only               |

> [!TIP]
> Enable **responsive visuals** and **lock aspect ratios** for cards.

---

## **12️⃣ PowerPoint Export Standards**

* Landscape 16:9
* One page per dashboard tab
* Titles auto-filled with last refresh date
* Font auto-embed: Tenorite / Afacad
* Header watermark: “Fabric Audit Dashboard | Confidential”

---

## **13️⃣ Accessibility & Readability**

| Element             | Guideline                  |
| ------------------- | -------------------------- |
| Color Contrast      | ≥ 4.5:1 ratio              |
| Font Size           | ≥ 10px everywhere          |
| Keyboard Navigation | All buttons labeled        |
| Alt Text            | Add chart descriptions     |
| Tooltip             | Concise: max 60 characters |

---

## **14️⃣ Layout Template (Reusable)**

```
/Governance/PowerBI/LayoutTemplates/
│
├── AuditOverview_Grid.pbit
├── CategoryDeepDive_Grid.pbit
├── EvidenceTracker_Grid.pbit
├── RemediationTracker_Grid.pbit
├── AuditCalendar_Grid.pbit
└── RiskMaturity_Grid.pbit
```

---

## **15️⃣ Quality Checklist**

| Checkpoint                            | Criteria       | Status |
| ------------------------------------- | -------------- | ------ |
| Layout spacing consistent (16px grid) | ✅              |        |
| Header alignment uniform              | ✅              |        |
| KPI font and color applied            | ✅              |        |
| Navigation bar functional             | ✅              |        |
| Mobile layout tested                  | ⚠️ Pending     |        |
| Accessibility labels added            | ⚠️ In Progress |        |

---

## **16️⃣ Snapshot Example (Textual Mockup)**

```
---------------------------------------------------------
|  FABRIC AUDIT DASHBOARD                               |
|  Governance | Compliance | Cost | Data Integrity       |
---------------------------------------------------------
|  KPI1  |  KPI2  |  KPI3  |  KPI4                     |
|-------------------------------------------------------|
| [Gauge Compliance]   [Bar Category Status]            |
| [Heatmap by Category] [Line Audit Score Trend]        |
---------------------------------------------------------
|  Footer: “Data from SharePoint – Refreshed: 09-Nov”   |
---------------------------------------------------------
```

---

## **17️⃣ Maintenance Tips**

* Export layout JSON from **Power BI Theme Designer** after every update.
* Maintain a **“v# Layout Changelog”** file noting grid changes.
* Assign layout ownership to **BI Governance Lead**.
* Validate alignment using **View → Snap to Grid** and **Align → Distribute Evenly**.

---

## **18️⃣ Outcome**

✅ A uniform, pixel-consistent visual identity for all governance dashboards.
✅ Easier cross-team collaboration (designs look identical across environments).
✅ Faster onboarding for new BI developers.
✅ Seamless PowerPoint and mobile exports.

---

Would you like me to create the **PowerPoint Deck Template** next — so audit and governance teams can export monthly summaries directly into a branded slide format with pre-linked KPI placeholders?
