# **Microsoft Fabric Dashboard Component Library**

*(Developer Reference for Reusable Power BI Visual Elements – Cards, Charts, and KPI Presets)*

---

## **1️⃣ Purpose**

The Component Library standardizes the **visual building blocks** used across Fabric dashboards.
Each component is pre-defined for **style, color, title logic, and DAX binding**, ensuring every developer produces identical visuals regardless of report type.

> [!NOTE]
> Use these presets when building *Governance*, *Cost*, or *Performance* dashboards to maintain UI consistency and reusability.

---

## **2️⃣ Component Structure**

Each reusable component includes:

1. **Component Name** – what it represents.
2. **Visual Type** – Power BI visual category.
3. **Use Case** – where and why it’s used.
4. **Data Binding** – exact fields or DAX measures.
5. **Formatting Rules** – color, font, alignment, icons.
6. **Optional Variations** – light vs dark header or compact version.

---

## **3️⃣ KPI Card Components**

### **A. CU Utilization Card**

| Attribute                 | Specification                             |
| ------------------------- | ----------------------------------------- |
| **Visual Type**           | Card                                      |
| **Title**                 | “CU Utilization (%)”                      |
| **Measure**               | `[CU Utilization %]`                      |
| **Font**                  | Segoe UI Semibold, 14pt                   |
| **Indicator Color Logic** | <80% → Green, 80–90% → Orange, >90% → Red |
| **Background**            | White `#FFFFFF`                           |
| **Border**                | 1px gray `#E1DFDD`, radius 4px            |
| **Icon**                  | “Gauge” (Lucide / Fluent icon set)        |

```DAX
CU Utilization % = 
DIVIDE(SUM(FactCapacityUsage[UsedCU]), SUM(FactCapacityUsage[AllocatedCU])) * 100
```

> [!TIP]
> Add a tooltip showing “Average CU last 7 days” using `AVERAGE(FactCapacityUsage[UsedCU])`.

---

### **B. Idle Hours Card**

| Attribute       | Specification                                  |
| --------------- | ---------------------------------------------- |
| **Title**       | “Idle Hours (Monthly)”                         |
| **Measure**     | `[Idle Hours]`                                 |
| **Color Logic** | 0–5 → Green, 6–10 → Orange, >10 → Red          |
| **Icon**        | “Clock”                                        |
| **Font**        | Segoe UI Semibold, 13pt                        |
| **Note**        | Highlight if idle >10% of total capacity hours |

```DAX
Idle Hours = 
SUMX(FactCapacityUsage, IF(FactCapacityUsage[UsedCU] = 0, 1, 0))
```

---

### **C. Total Cost Card**

| Visual | Card |
| Title | “Monthly Cost (₹)” |
| Measure | `[Cost per Workspace (INR)]` |
| Format | Currency (INR), no decimals |
| Font | Segoe UI Semibold, 14pt |
| Icon | “CurrencyRupee” |
| Color | Fabric Blue (#0078D4) |
| Tooltip | “Total monthly CU cost across all workspaces.” |

---

### **D. Budget Variance Card**

| Visual | Card |
| Title | “Budget Variance (%)” |
| Measure | `[Budget Variance %]` |
| Color Logic | <10% → Green, 10–20% → Orange, >20% → Red |
| Icon | “TrendingUp” |
| Format | Percentage |
| Tooltip | “(Actual – Budget) / Budget × 100” |

---

## **4️⃣ Chart Components**

### **A. CU Utilization Trend**

| Type | Line Chart |
| X-Axis | `DimDate[Date]` |
| Y-Axis | `[CU Utilization %]` |
| Color | Fabric Blue |
| Line Width | 2pt |
| Tooltip | Date, CU %, CostINR |
| Smoothing | ON |
| Forecast | 7-day forecast enabled |

---

### **B. Cost by Department**

| Type | Donut Chart |
| Field | `DimWorkspace[Department]` |
| Values | `[Cost per Workspace (INR)]` |
| Color Palette | Fabric Accent (`#0078D4`, `#5C9DD5`, `#9CC3E4`, `#C8DDF2`) |
| Data Labels | % inside slices |
| Legend | Right |
| Tooltip | Department, Cost (₹) |

---

### **C. Cost by Workspace Table**

| Type | Table |
| Columns | Workspace, Department, CU%, Idle Hours, CostINR, Efficiency Index |
| Conditional Formatting |
• CU%: Gradient Green→Amber→Red
• Idle Hours: Red if >10
| Header Font | Segoe UI Semibold, 11pt |
| Body Font | Segoe UI, 10pt |
| Border | None |
| Alt Row Color | `#FAFAFA` |

---

### **D. Job Duration Scatter**

| Type | Scatter Plot |
| X | `[DurationMinutes]` |
| Y | `[UsedCU]` |
| Details | `FactJobExecutions[JobType]` |
| Color | JobType (categorical) |
| Trend Line | Enabled |
| Tooltip | Job ID, Workspace, Duration, CU |

> [!TIP]
> Highlight points where Duration > 60 mins in red for quick anomaly spotting.

---

## **5️⃣ Matrix Components**

### **A. Efficiency Heatmap**

| Type | Matrix |
| Rows | Department |
| Columns | Workspace |
| Values | `[CU Utilization %]` |
| Conditional Format | Gradient: `#C8DDF2` → `#0078D4` |
| Font | Segoe UI 10pt |
| Totals | ON |
| Tooltip | “Department average CU: xx%” |

---

## **6️⃣ Forecast and Alert Components**

### **A. CU Forecast Line**

| Type | Line Chart |
| Fields | Date, `[CU Utilization %]` |
| Forecast Horizon | 7 days |
| Confidence Interval | 80% |
| Line Color | Blue, dashed forecast section |
| Tooltip | Date, Forecast CU%, Upper/Lower Bound |

---

### **B. Alert Table**

| Type | Table |
| Columns | AlertType, Severity, Workspace, Timestamp, Owner |
| Conditional Formatting |
• Severity = High → Red
• Severity = Medium → Orange
| Header Background | Light Gray (#F3F2F1) |
| Row Striping | Enabled |
| Font | Segoe UI 10pt |
| Tooltip | Alert Description |

---

### **C. KPI Summary Matrix**

| Type | Matrix |
| Rows | KPI Category (Utilization, Idle, Cost, Variance) |
| Columns | Actual, Target, Status |
| Conditional Formatting |
Green if within target, Red if breach |
| Font | 11pt |
| Totals | Off |

---

## **7️⃣ Visual Titles and Dynamic Text**

Use **dynamic titles** for contextual filtering:

```DAX
="Fabric Cost Summary – " & SELECTEDVALUE(DimDate[Month]) 
```

For department-specific views:

```DAX
="Department: " & SELECTEDVALUE(DimWorkspace[Department])
```

> [!NOTE]
> Avoid fixed month names — dynamic titles make dashboards reusable year-round.

---

## **8️⃣ Icon Library**

| Icon              | Use Case            | Source              |
| ----------------- | ------------------- | ------------------- |
| **Gauge**         | CU Utilization      | Fluent System Icons |
| **Clock**         | Idle Hours          | Lucide              |
| **CurrencyRupee** | Cost                | Fluent              |
| **TrendingUp**    | Variance            | Lucide              |
| **Database**      | Capacity Metrics    | Fluent              |
| **Lightning**     | Alerts / Overload   | Lucide              |
| **Calendar**      | Forecast / Timeline | Fluent              |
| **Users**         | Access Summary      | Fluent              |

All icons standardized to **24×24 px**, color **#0078D4**, opacity 85%.

---

## **9️⃣ Tooltip Templates**

### **Cost Tooltip**

```
Workspace: [WorkspaceName]
Department: [Department]
Monthly Cost: ₹[CostINR]
CU Utilization: [CU Utilization %]%
```

### **Pipeline Tooltip**

```
Pipeline: [PipelineName]
Run Duration: [DurationMinutes] mins
Status: [RunStatus]
```

### **Alert Tooltip**

```
Alert Type: [AlertType]
Severity: [Severity]
Detected On: [Date]
```

> [!TIP]
> Keep tooltips under 5 lines for readability — never exceed 150 characters.

---

## **🔟 Predefined Visual Groups (Bundles)**

| Bundle Name             | Components                                      | Use                         |
| ----------------------- | ----------------------------------------------- | --------------------------- |
| **Executive KPI Deck**  | CU Card + Idle Card + Cost Card + Variance Card | Top-row summary             |
| **Cost Analysis Block** | Donut (Dept) + Line (Trend) + Table (Workspace) | Page 2 – cost attribution   |
| **Performance Block**   | Scatter (CU vs Duration) + Matrix (Efficiency)  | Page 4 – performance review |
| **Forecast Block**      | Line (Forecast) + Alert Table                   | Page 5 – predictive view    |

> [!IMPORTANT]
> Each “block” should fit within a 2×2 grid cell and align to standard page margins.

---

## **11️⃣ Developer Naming Conventions**

| Element      | Format                 | Example              |
| ------------ | ---------------------- | -------------------- |
| Measure      | `m_[Topic]_[Metric]`   | `m_CU_Utilization`   |
| Table Visual | `tbl_[Topic]_[Detail]` | `tbl_Workspace_Cost` |
| Chart Visual | `cht_[Metric]_[Type]`  | `cht_CU_Trend_Line`  |
| KPI Card     | `kpi_[Metric]`         | `kpi_BudgetVariance` |
| Matrix       | `mtx_[Topic]_[Metric]` | `mtx_Efficiency_CU`  |

Store definitions in a “Measure Table” named **_Fabric_Metrics**.

---

## **12️⃣ Implementation Notes**

* Maintain all DAX logic in the centralized **Metrics Table**.
* Store reusable visuals in **Power BI Template (.pbit)** format under:
  `/OneLake/Governance/Templates/Visual_Components/`
* Assign consistent bookmarks and filters to each visual group.
* Use **Themes** from `Fabric_Governance_Theme.json` for palette alignment.

---

## **13️⃣ Reuse Guidelines**

✅ Reuse across dashboards:

* Governance
* Cost Optimization
* Capacity Monitoring
* Pipeline Performance
* Finance Chargeback Reports

❌ Don’t reuse in:

* Ad hoc analytics (non-governed Power BI files)
* External client dashboards (branding mismatch)

---

> [!FINAL NOTE]
> This Component Library ensures every Fabric dashboard — whether built by a developer, analyst, or consultant — stays visually and functionally consistent.
> Each new dashboard should start from this library’s template rather than building visuals manually.

---

Would you like me to create a **Fabric Governance + Cost Dashboard Developer Handbook** next — a full guide combining your theme, component library, and DAX templates into a single 30–40 page build manual for internal BI teams?
