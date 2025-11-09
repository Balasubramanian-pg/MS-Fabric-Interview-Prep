# **Fabric Audit Dashboard – Power BI JSON Theme**

*(Design specification for consistent visuals, typography, and compliance color semantics)*

Save this as:
`/Governance/DashboardSpecs/Fabric_AuditDashboard_Theme.json`

---

```json
{
  "name": "Fabric Audit Dashboard Theme v1.0",
  "dataColors": [
    "#16A34A",   // Compliant
    "#FACC15",   // Partial
    "#DC2626",   // Non-Compliant
    "#2563EB",   // Verified
    "#9CA3AF",   // Pending
    "#0E7490",   // Information
    "#92400E"    // Warning
  ],
  "background": "#F8FAFC",
  "foreground": "#111827",
  "tableAccent": "#2563EB",

  "visualStyles": {
    "*": {
      "general": [{ "outlineColor": "#E5E7EB", "outlineWeight": 0 }],
      "titleText": [{ "fontFamily": "Tenorite", "fontSize": 13, "color": "#111827" }],
      "labelText": [{ "fontFamily": "Afacad", "fontSize": 11, "color": "#374151" }],
      "header": [{ "fontSize": 12, "fontFamily": "Tenorite", "color": "#1F2937" }],
      "subTitleText": [{ "fontFamily": "Afacad", "color": "#4B5563", "fontSize": 10 }]
    },

    "card": {
      "*": {
        "labels": [{ "color": "#111827", "fontFamily": "Afacad", "fontSize": 12 }],
        "calloutValue": [{ "color": "#2563EB", "fontSize": 22, "fontFamily": "Tenorite" }],
        "background": [{ "color": "#FFFFFF" }]
      }
    },

    "table": {
      "*": {
        "grid": [{ "outlineColor": "#E5E7EB" }],
        "rowHeaders": [{ "fontSize": 11, "color": "#111827" }],
        "values": [{ "fontSize": 10, "color": "#374151" }]
      }
    },

    "matrix": {
      "*": {
        "grid": [{ "outlineColor": "#E5E7EB" }],
        "rowHeaders": [{ "fontFamily": "Afacad", "color": "#111827" }],
        "values": [{ "fontSize": 10, "color": "#374151" }]
      }
    },

    "textbox": {
      "*": {
        "general": [{ "fontFamily": "Afacad", "color": "#1F2937", "fontSize": 12 }]
      }
    },

    "slicer": {
      "*": {
        "items": [{ "color": "#111827", "fontSize": 11 }],
        "background": [{ "color": "#FFFFFF" }],
        "outlineColor": [{ "color": "#D1D5DB" }]
      }
    },

    "shape": {
      "*": {
        "color": [{ "color": "#2563EB" }]
      }
    },

    "lineChart": {
      "*": {
        "dataLabels": [{ "fontSize": 10, "color": "#111827" }],
        "categoryLabels": [{ "fontSize": 10, "color": "#4B5563" }],
        "legend": [{ "fontSize": 10, "color": "#111827" }],
        "referenceLine": [{ "color": "#DC2626", "lineStyle": "dashed" }]
      }
    },

    "barChart": {
      "*": {
        "dataLabels": [{ "fontSize": 10, "color": "#111827" }],
        "categoryLabels": [{ "fontSize": 10, "color": "#374151" }],
        "valueAxis": [{ "fontSize": 10, "color": "#4B5563" }],
        "legend": [{ "fontSize": 10, "color": "#111827" }]
      }
    },

    "gauge": {
      "*": {
        "labels": [{ "fontSize": 11, "color": "#1F2937" }],
        "axis": [{ "color": "#9CA3AF" }],
        "targetLine": [{ "color": "#DC2626" }]
      }
    },

    "scatterChart": {
      "*": {
        "dataLabels": [{ "fontSize": 9, "color": "#1F2937" }],
        "legend": [{ "fontSize": 10, "color": "#111827" }]
      }
    },

    "donutChart": {
      "*": {
        "dataLabels": [{ "fontSize": 11, "color": "#111827" }],
        "legend": [{ "fontSize": 10, "color": "#1F2937" }],
        "innerRadius": [{ "percent": 55 }]
      }
    }
  },

  "textClasses": {
    "title": { "fontFamily": "Tenorite", "fontSize": 14, "color": "#111827" },
    "header": { "fontFamily": "Afacad", "fontSize": 13, "color": "#1F2937" },
    "body": { "fontFamily": "Afacad", "fontSize": 11, "color": "#374151" },
    "caption": { "fontFamily": "Afacad", "fontSize": 10, "color": "#6B7280" }
  }
}
```

---

## **Usage Instructions**

1. Save as `Fabric_AuditDashboard_Theme.json`.
2. In Power BI → **View → Themes → Browse for themes** → Import this JSON.
3. Confirm that:

   * Cards show blue metrics (#2563EB)
   * Compliance colors align (Green, Amber, Red)
   * Fonts load as *Tenorite* (titles) and *Afacad* (labels/body)

> [!TIP]
> To enforce consistent visuals across all dashboards, store this file in:
> `/Governance/ThemeLibrary/`
> and set it as default via Power BI Service “Manage Theme” in workspace settings.

---

Would you like me to make the **“Fabric Audit Dashboard – UI Layout Wireframe”** next? (It would visually describe how to arrange visuals, KPIs, and navigation in the dashboard grid layout.)
