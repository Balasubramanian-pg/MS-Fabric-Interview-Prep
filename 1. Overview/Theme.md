Alright — here’s the **Power BI JSON theme file** you can directly import into Power BI Desktop.
It matches Microsoft Fabric’s clean blue-gray palette, with consistent KPI color logic, readable fonts, and neutral backgrounds suited for enterprise dashboards.

---

```json
{
  "name": "Fabric Cost Governance Theme",
  "dataColors": [
    "#0078D4", "#5C9DD5", "#9CC3E4", "#C8DDF2",
    "#E81123", "#FF8C00", "#107C10", "#8E8CD8"
  ],
  "background": "#F5F6F8",
  "foreground": "#323130",
  "tableAccent": "#0078D4",
  "visualStyles": {
    "*": {
      "*": {
        "title": [
          {
            "color": { "solid": { "color": "#323130" } },
            "fontFamily": "Segoe UI Semibold",
            "fontSize": 13
          }
        ],
        "labels": [
          {
            "color": { "solid": { "color": "#505050" } },
            "fontFamily": "Segoe UI",
            "fontSize": 10
          }
        ]
      }
    },
    "card": {
      "*": {
        "background": [
          { "solid": { "color": "#FFFFFF" } }
        ],
        "labels": [
          {
            "color": { "solid": { "color": "#323130" } },
            "fontFamily": "Segoe UI Semibold",
            "fontSize": 12
          }
        ]
      }
    },
    "gauge": {
      "*": {
        "axis": [
          { "color": { "solid": { "color": "#C8C6C4" } } }
        ],
        "dataColors": [
          { "solid": { "color": "#0078D4" } }
        ]
      }
    },
    "table": {
      "*": {
        "grid": [
          { "color": { "solid": { "color": "#E1DFDD" } } }
        ],
        "values": [
          {
            "color": { "solid": { "color": "#323130" } },
            "fontFamily": "Segoe UI",
            "fontSize": 10
          }
        ]
      }
    },
    "slicer": {
      "*": {
        "background": [
          { "solid": { "color": "#FFFFFF" } }
        ],
        "text": [
          {
            "color": { "solid": { "color": "#323130" } },
            "fontFamily": "Segoe UI",
            "fontSize": 11
          }
        ]
      }
    },
    "shape": {
      "*": {
        "fill": [
          { "solid": { "color": "#0078D4" } }
        ]
      }
    },
    "kpi": {
      "*": {
        "indicator": [
          { "color": { "solid": { "color": "#0078D4" } } }
        ],
        "trend": [
          { "color": { "solid": { "color": "#8E8CD8" } } }
        ]
      }
    }
  },
  "textClasses": {
    "header": { "fontSize": 16, "fontFamily": "Segoe UI Semibold", "color": "#323130" },
    "label": { "fontSize": 11, "fontFamily": "Segoe UI", "color": "#505050" }
  },
  "kpiColorCoding": {
    "good": "#107C10",
    "neutral": "#FF8C00",
    "bad": "#E81123"
  }
}
```

---

### **Theme Characteristics**

* **Primary color:** Fabric Blue `#0078D4`
* **Accent colors:** Soft gradient blues for CU and cost visuals
* **Negative indicator:** Red `#E81123`
* **Warning:** Orange `#FF8C00`
* **Positive:** Green `#107C10`
* **Fonts:** Segoe UI (consistent with Microsoft design)
* **Background:** Subtle light gray `#F5F6F8` for readability

> [!TIP]
> After importing:
> *File → Options → Report Settings → “Use report theme” → Import JSON*
> Then adjust KPI cards to use color logic:
>
> * Green < 80% CU
> * Amber 80–90% CU
> * Red > 90% CU

---

Would you like me to create a **Fabric Dashboard Style Guide** next — a one-pager defining visual hierarchy, typography, spacing, and KPI color usage for consistency across all your Power BI dashboards?
