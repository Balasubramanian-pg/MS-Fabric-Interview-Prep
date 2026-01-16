# What is 'Universal Security' in Fabric?

**Universal Security** (officially referred to as **OneLake Security**) is a centralized security model in Microsoft Fabric that allows you to define data access rules once and have them enforced across every engine in the platform.

Before this, you often had to set up Row-Level Security (RLS) separately for SQL, again for Power BI, and again for Spark. With Universal Security, the "source of truth" for security sits directly in OneLake.

---

### How It Works: "Define Once, Enforce Everywhere"

The core philosophy is that security should be tied to the **data**, not the **tool** used to access it. When you configure a security role in a Lakehouse or Warehouse, those rules are natively respected by:

* **Power BI** (via Direct Lake mode)
* **SQL Analytics Endpoints**
* **Spark Notebooks**
* **OneLake Explorer**

### Key Capabilities

* **Unified Row-Level Security (RLS):** You can restrict which rows a user can see (e.g., "Sales Managers can only see data from their own region"). This logic is enforced whether they query via a SQL script or a Power BI report.
* **Column-Level Security (CLS):** You can hide sensitive columns (like Social Security numbers or personal phone numbers) from specific users.
* **Automatic Enforcement in Spark:** Traditionally, Spark has been difficult to secure at a granular level. Fabric uses a "secure gateway" to ensure Spark jobs only "see" the data the user is authorized to access.
* **No Data Duplication:** Since the security is applied at the OneLake level, you don't need to create filtered "views" or duplicate data into different tables for different user groups.

---

### Comparison: Old Model vs. Universal Security

| Feature | Traditional Model (Siloed) | Universal Security (Fabric) |
| --- | --- | --- |
| **Setup** | Manually configured in each engine. | Configured once at the OneLake level. |
| **Consistency** | High risk of rules drifting apart. | Guaranteed consistency across all tools. |
| **Spark Security** | Hard to manage (usually all-or-nothing). | Granular RLS/CLS natively supported. |
| **Maintenance** | High (updating 3+ systems for 1 change). | Low (one central update). |

### Important Requirement: "User Identity" Mode

For Universal Security to work on SQL endpoints, you must switch the endpoint to **User Identity** mode. This ensures that the engine "impersonates" the person running the query, checking their specific OneLake permissions rather than using a shared background service account.

[OneLake Security in Microsoft Fabric](https://www.youtube.com/watch?v=g0ohM-NLZgk)

This video highlights the major announcements regarding OneLake Security, explaining how it simplifies enterprise data governance by unifying RLS and CLS across the entire Fabric ecosystem.

**Would you like to see a step-by-step example of how to write a DAX or SQL filter for Row-Level Security in this new model?**
