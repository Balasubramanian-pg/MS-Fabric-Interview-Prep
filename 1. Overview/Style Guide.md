# **Microsoft Fabric Dashboard Style Guide**

*(Enterprise Power BI Visual Design Standards for Governance, Cost, and Operations Dashboards)*

---

## **1️⃣ Purpose**

This style guide defines the **visual identity, layout standards, and UX consistency rules** for all Microsoft Fabric–based Power BI dashboards.
It ensures every report — from governance to cost tracking — feels coherent, readable, and enterprise-ready.

> [!NOTE]
> A consistent visual language helps leadership absorb data faster and builds long-term trust in the dashboards.

---

## **2️⃣ Core Visual Identity**

| Element                   | Specification                   | Notes                                  |
| ------------------------- | ------------------------------- | -------------------------------------- |
| **Primary Color**         | `#0078D4` (Fabric Blue)         | For highlights, charts, and titles     |
| **Accent Palette**        | `#5C9DD5`, `#9CC3E4`, `#C8DDF2` | Used for gradients and stacked visuals |
| **Positive KPI**          | `#107C10` (Green)               | Performance within or above target     |
| **Warning KPI**           | `#FF8C00` (Orange)              | Approaching threshold                  |
| **Critical KPI**          | `#E81123` (Red)                 | Breach or failure                      |
| **Background**            | `#F5F6F8`                       | Neutral gray for low eye fatigue       |
| **Grid Lines / Dividers** | `#E1DFDD`                       | Subtle boundary without clutter        |

---

## **3️⃣ Typography**

| Text Type               | Font              | Size     | Weight    | Use                    |
| ----------------------- | ----------------- | -------- | --------- | ---------------------- |
| **Report Title**        | Segoe UI Semibold | 18–20 pt | Bold      | Page header            |
| **Section Header**      | Segoe UI Semibold | 14 pt    | Semi-Bold | Group title            |
| **Labels / Values**     | Segoe UI          | 10–11 pt | Regular   | Chart labels, tooltips |
| **Cards / KPIs**        | Segoe UI Semibold | 12–13 pt | Semi-Bold | Metric emphasis        |
| **Footnotes / Sources** | Segoe UI Light    | 9 pt     | Light     | Subtext or disclaimers |

> [!TIP]
> Keep font variety minimal: one typeface, two weights. Bold sparingly — only for numbers and key labels.

---

## **4️⃣ Layout and Spacing**

* **Canvas Size:** 16:9 ratio, 1600×900 px.
* **Margins:** 20 px outer margin, 10 px between visuals.
* **Grid:** 3×3 or 4×2 modular grid; align all visuals to this grid.
* **Card Row Height:** 120–140 px.
* **Chart Row Height:** 300–350 px.
* **Footer (optional):** 40 px tall, aligned left with logo.

```text
[Header Row]  → Report Title + Filters
[Metrics Row] → KPI Cards (3–4)
[Charts Row]  → Line / Bar / Treemap
[Detail Row]  → Tables / Heatmaps
[Footer]      → Notes / Branding
```

> [!NOTE]
> Every page should tell one narrative — limit to **12 visuals max**.

---

## **5️⃣ KPI and Card Design**

| Element             | Rule                                 | Example                          |
| ------------------- | ------------------------------------ | -------------------------------- |
| **Background**      | Pure white `#FFFFFF`                 | Matches with gray canvas         |
| **Border Radius**   | 4 px                                 | Subtle shadow for depth          |
| **Title Color**     | `#323130`                            | Consistent with text hierarchy   |
| **KPI Color Logic** | Green < 80%, Amber 80–90%, Red > 90% | Visual cue for thresholds        |
| **Number Format**   | 1 decimal, no trailing zero          | Clean reading                    |
| **Icons**           | Lucide or Fluent System Icons        | Minimal only — no decorative art |

> [!TIP]
> For clarity, prefix metrics with units (₹, %, hrs) inside card title.

---

## **6️⃣ Charting Guidelines**

### **Bar / Column Charts**

* Max 8–10 bars visible.
* Use **Fabric Blue** for primary, **Gray** for secondary.
* Show **data labels** on bars above 10%.
* Avoid stacked bars unless showing share.

### **Line / Area Charts**

* Use thick solid lines (`2pt`) for actuals, dashed (`1pt`) for forecasts.
* Highlight target line in **light gray** (`#C8C6C4`).
* Smooth area fill: 15–20% opacity.

### **Pie / Donut Charts**

* Limit to 5 slices; use **Fabric accent palette**.
* Center label: % or absolute value only, not both.

### **Tables / Matrices**

* Zebra striping on alternate rows.
* Header font bold, white background.
* Use **conditional formatting** for variance columns.

### **Heatmaps**

* Gradient from `#C8DDF2` (low) → `#0078D4` (high).
* Add numeric overlay for quick read.

> [!CAUTION]
> Avoid dark backgrounds — they distort Fabric blues on projection screens.

---

## **7️⃣ Filters and Navigation**

* Global filters always in **top right** (Date, Department, Environment).
* Use **Slicer panels** (white background, 11 pt font).
* Bookmark buttons:

  * **Overview**
  * **Cost**
  * **Performance**
  * **Alerts**
* Icons: gray outlines with hover color `#0078D4`.
* Navigation bar height: 40 px, consistent across pages.

---

## **8️⃣ Accessibility & Readability**

* Minimum contrast ratio: 4.5:1 for text.
* Avoid red/green dependency — pair with shape or label.
* Tooltips mandatory for all KPIs.
* Alt text on images and logos.
* Default zoom: 100%; no scrolling visuals.

> [!TIP]
> Use “View → Page View → Fit to Page” before publishing to ensure responsiveness.

---

## **9️⃣ Consistency Checklist**

| Check            | Description                     | Status |
| ---------------- | ------------------------------- | ------ |
| Title placement  | Left-aligned, same size         | ✅      |
| Color scheme     | Only Fabric blue palette        | ✅      |
| Fonts            | Segoe UI across visuals         | ✅      |
| Background       | #F5F6F8 only                    | ✅      |
| Units consistent | INR, %, hrs standardized        | ✅      |
| Navigation       | Same icons, same layout         | ✅      |
| Alert colors     | Unified logic (green/amber/red) | ✅      |

---

## **🔟 Branding and Footer**

* Add company logo (40×40 px) on top-left corner.
* Footer: “Powered by Microsoft Fabric Governance Framework”

  * Font: Segoe UI, 9 pt, Gray `#605E5C`.
* Optional watermark: company domain in light gray, bottom-right.

---

## **11️⃣ Dos & Don’ts**

✅ **Do**

* Use whitespace generously.
* Keep colors minimal and meaningful.
* Group visuals logically (by KPI theme).
* Label all visuals clearly.

❌ **Don’t**

* Overuse gradients or 3D effects.
* Mix random palettes.
* Hide context (e.g., unlabeled axes).
* Use emojis or decorative icons.

---

## **12️⃣ Quick Visual Reference**

| Type        | Recommended Colors                  | Font Size | Notes                      |
| ----------- | ----------------------------------- | --------- | -------------------------- |
| KPI Card    | White background, colored indicator | 12pt      | One metric per card        |
| Line Chart  | Fabric Blue                         | 11pt      | Use legend on right        |
| Table       | Gray header                         | 10pt      | Conditional variance color |
| Donut Chart | Fabric Blue accents                 | 10pt      | Max 5 categories           |
| Heatmap     | Blue gradient                       | 11pt      | Numeric overlay on cells   |

---

## **13️⃣ Publishing Standards**

* Use **Direct Lake** for large Fabric datasets.
* Embed via **Teams**, **SharePoint**, or **Power BI App workspace**.
* Apply **role-based view** (CIO, Finance, Ops).
* Refresh daily (set 4:00 AM UTC).
* Validate rendering on desktop + web.

---

> [!IMPORTANT]
> This guide should be **version-controlled** and stored under
> `/Governance/Design_Standards/Fabric_Dashboard_StyleGuide_v1.0.pdf` in OneLake.

---

Would you like me to build the **“Fabric Dashboard Component Library”** next — a developer reference showing reusable card, chart, and KPI visual presets (titles, colors, icons, and measure bindings)?
