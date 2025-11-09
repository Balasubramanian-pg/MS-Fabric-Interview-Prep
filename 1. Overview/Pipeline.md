# **Microsoft Fabric Data Flow Example – End-to-End Pipeline**

Below is a step-by-step **textual data flow + command sequence** that shows how data moves from ingestion to Power BI visualization inside Microsoft Fabric.
It combines **SQL, PySpark, and KQL** snippets to mirror real-life operations.

---

## **1. Data Ingestion (Data Factory)**

**Scenario:** Import daily sales data from an Azure SQL Database into OneLake.

### **Data Factory Setup**

* **Source:** Azure SQL Database (`SalesDB`)
* **Sink:** OneLake Delta Table (`Retail/Sales/Raw`)

**Pipeline Steps**

1. Create new pipeline in *Data Factory (Gen2)*.
2. Add **Copy Data Activity**:

   * **Source:** `SELECT * FROM Sales.Orders WHERE OrderDate = GETDATE()`
   * **Sink Type:** OneLake (Delta format)
3. Schedule refresh every night at 1:00 AM.

```json
{
  "source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM Sales.Orders WHERE OrderDate = GETDATE()"
  },
  "sink": {
    "type": "OneLakeSink",
    "tableName": "Retail_Sales_Raw",
    "format": "delta"
  },
  "schedule": "Daily @ 1AM"
}
```

> [!NOTE]
> Data Factory (Gen2) supports over **200+ connectors** and can load into OneLake directly in **Delta format**—no conversion step needed.

---

## **2. Data Storage (OneLake)**

* The ingested data lands in OneLake under the path:

  ```
  /Retail/Sales/Raw/2025/11/Orders.delta
  ```

* You can verify using SQL or PySpark:

```sql
SELECT COUNT(*) 
FROM OneLake.Retail.Sales.Raw;
```

or

```python
df = spark.read.format("delta").load("OneLake/Retail/Sales/Raw")
print(df.count())
```

> [!IMPORTANT]
> Every workspace in Fabric has a **dedicated OneLake folder** automatically linked to it.
> No manual provisioning required.

---

## **3. Data Transformation (Synapse Data Engineering)**

**Objective:** Clean and enrich the raw data before loading into analytics layer.

```python
# Load raw data
raw_df = spark.read.format("delta").load("OneLake/Retail/Sales/Raw")

# Clean data
clean_df = raw_df.filter("Status == 'Completed'") \
                 .withColumnRenamed("OrderAmount", "SalesAmount")

# Aggregate
agg_df = clean_df.groupBy("Region", "ProductCategory") \
                 .sum("SalesAmount") \
                 .withColumnRenamed("sum(SalesAmount)", "TotalSales")

# Save transformed data
agg_df.write.format("delta").mode("overwrite").save("OneLake/Retail/Sales/Transformed")
```

> [!TIP]
> Use Spark DataFrames for distributed transformation.
> Save results in Delta format for compatibility across all Fabric workloads.

---

## **4. Data Warehousing (Synapse Data Warehouse)**

**Goal:** Expose the transformed data for analytics in T-SQL layer.

```sql
CREATE TABLE FabricWarehouse.Retail.SalesSummary
USING DELTA
LOCATION 'OneLake/Retail/Sales/Transformed';

SELECT TOP 10 * 
FROM FabricWarehouse.Retail.SalesSummary;
```

* Fabric automatically recognizes Delta tables from OneLake.
* No need to import data—**metadata linking** is sufficient.

> [!IMPORTANT]
> The same table can be accessed by both **T-SQL** and **PySpark** simultaneously—thanks to Delta Lake interoperability.

---

## **5. Real-Time Analytics (Synapse Real-Time Analytics)**

**Objective:** Combine live event data (store footfall, IoT) with sales aggregates.

```kql
FootfallStream
| where StoreID in ("BLR01", "DEL02")
| summarize TotalVisits = count(), LastSeen = max(Timestamp) by StoreID
| join kind=inner (SalesSummary) on StoreID
| project StoreID, TotalSales, TotalVisits, round(TotalSales / TotalVisits, 2) as ConversionRate
```

> [!NOTE]
> KQL supports sub-second analytics on streaming datasets—ideal for dashboards and alerts.

---

## **6. Data Visualization (Power BI)**

**Setup in Power BI:**

* Connect dataset to OneLake using **Direct Lake Mode**.
* Select **Retail/Sales/Transformed** Delta table.
* Build visuals:

  * **Bar Chart:** Total Sales by Product Category
  * **KPI Card:** Conversion Rate (from KQL query)

**Example DAX Measure:**

```DAX
Total Sales = SUM('SalesSummary'[TotalSales])
```

**Result:** Dashboards show near real-time sales trends with no refresh lag.

> [!TIP]
> Direct Lake Mode queries Delta tables directly from OneLake — no data import or refresh required.

---

## **7. Automation (Data Activator)**

**Scenario:** Alert if any store’s conversion rate drops below 1%.

**Trigger Definition:**

* **Input:** KQL result table from Real-Time Analytics.
* **Condition:** `ConversionRate < 1`.
* **Action:** Send Teams message to “Retail Ops Channel”.

```json
{
  "trigger": "ConversionRate < 1",
  "action": {
    "type": "TeamsMessage",
    "target": "Retail-Ops",
    "message": "Store {{StoreID}} Conversion Rate dropped below 1%"
  }
}
```

> [!CAUTION]
> Set thresholds carefully—overly sensitive triggers can create false positives or alert fatigue.

---

## **8. Governance and Security (Purview + Fabric Admin)**

* **Purview Lineage View:**

  * Shows how data moved:
    `SQL DB → Data Factory → OneLake → Synapse → Power BI → Data Activator`
* **Security Enforcement:**

  * Row-Level Security (RLS) ensures analysts see only regional data.
  * Managed centrally—applies to Power BI, Synapse, and even KQL datasets.

> [!IMPORTANT]
> You define security **once at OneLake or workspace level**—Fabric propagates it to all connected tools.

---

## **9. End-to-End Summary**

| Stage     | Component                   | Data Movement         | Language / Interface   |
| --------- | --------------------------- | --------------------- | ---------------------- |
| Ingest    | Data Factory                | SQL DB → OneLake      | Visual Pipeline / JSON |
| Store     | OneLake                     | Unified Delta Lake    | Delta Tables           |
| Transform | Synapse Data Engineering    | OneLake → OneLake     | PySpark                |
| Warehouse | Synapse Data Warehouse      | OneLake → SQL         | T-SQL                  |
| Stream    | Synapse Real-Time Analytics | IoT Stream → KQL DB   | KQL                    |
| Visualize | Power BI                    | OneLake (Direct Lake) | DAX                    |
| Automate  | Data Activator              | Power BI → Teams      | No-code                |
| Govern    | Purview                     | All                   | Unified Security       |

> [!NOTE]
> All components operate **on the same data copy** in OneLake—this is the defining feature of Microsoft Fabric.

---

## **10. Quick Fabric Command Summary**

| Task                   | Sample Command / Action                          |
| ---------------------- | ------------------------------------------------ |
| Load data into OneLake | `Data Factory Pipeline → Sink: Delta Table`      |
| Read data in Spark     | `spark.read.format("delta").load("OneLake/...")` |
| Create warehouse table | `CREATE TABLE ... USING DELTA`                   |
| Query streaming data   | `KQL: summarize count() by EventType`            |
| Connect Power BI       | Choose **Direct Lake Mode**                      |
| Trigger alert          | Create rule in **Data Activator**                |
| Audit lineage          | View in **Microsoft Purview**                    |

---

## **Final Flow Recap**

```text
[SQL Source] 
   ↓
[Data Factory (ETL)]
   ↓
[OneLake Delta Storage]
   ↓
[Synapse Data Engineering] → [Synapse Data Science] → [Synapse Data Warehouse]
   ↓                               ↓
   └──────────────► [Synapse Real-Time Analytics] 
                                ↓
                             [Power BI Visualization]
                                ↓
                             [Data Activator Alert]
                                ↓
                          [Teams / Power Automate Action]
```

---

> [!IMPORTANT]
> Every Fabric workload (Spark, SQL, KQL, BI, Automation) operates **within one SaaS-managed workspace**, backed by **OneLake** and governed through **Purview** — enabling a **single source of truth**, faster analytics, and automated decision-making.

---

Would you like me to follow this up with a **real-world enterprise case study** (for example, “Retail Analytics Implementation in Microsoft Fabric”) that ties all this into business impact and architecture choices?
