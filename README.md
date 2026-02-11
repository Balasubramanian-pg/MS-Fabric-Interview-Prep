# MS-Fabric-Interview-Prep
Like are they even any interview questions?

### **1. Microsoft Fabric Overview**
**Core Components**:
| Component | Purpose |
|-----------|---------|
| **OneLake** | Unified data lake (Delta format). |
| **Data Factory** | ETL/ELT pipelines. |
| **Synapse Data Engineering** | Spark notebooks, Spark jobs. |
| **Synapse Data Science** | ML models, experiments. |
| **Synapse Data Warehousing** | T-SQL-based warehousing. |
| **Synapse Real-Time Analytics** | KQL databases, event streaming. |
| **Power BI** | Visualization & reporting. |
| **Data Activator** | Real-time triggers (no-code). |

**Key Features**:
- **Unified governance**: Single security/model across all workloads.
- **Direct Lake Mode**: Power BI connects directly to OneLake (no data duplication).
- **Shortcuts**: Virtualize data (no physical copy).

### **2. OneLake**
**Basics**:
- **Storage**: Delta Parquet format (open standard).
- **Hierarchy**:
  ```plaintext
  /<Workspace>
    /<Lakehouse>
      /Tables
      /Files
    /<Warehouse>
    /<KQL Database>
  ```
- **Access**: Via T-SQL, Spark, Power BI, or REST APIs.

**Shortcuts**:
- **Create Shortcut**:
  ```sql
  CREATE SHORTcut ADLSGen2Storage
  WITH (STORAGEACCOUNT = 'storageaccount', CONTAINER = 'container');
  ```
- **Types**: Azure Data Lake, AWS S3, Google Cloud Storage.

**Permissions**:
- **Roles**: Admin, Contributor, Viewer, Custom (Azure RBAC).
- **Grant Access**:
  ```sql
  GRANT SELECT ON TABLE Sales TO USER 'user@domain.com';
  ```

### **3. Data Factory (Pipelines)**
**Key Concepts**:
- **Pipelines**: Workflow of activities (e.g., copy, transform).
- **Activities**:
  | Activity | Use Case |
  |----------|----------|
  | **Copy Data** | Move data between sources. |
  | **Data Flow** | Transform data (mapping/data wrangling). |
  | **Notebook** | Run Spark code. |
  | **Stored Procedure** | Execute SQL/T-SQL. |
  | **Web Activity** | Call REST APIs. |

**Copy Data Example**:
```json
{
  "source": {
    "type": "DelimitedTextSource",
    "storeSettings": {
      "type": "AzureBlobFSReadSettings",
      "path": "container/input.csv"
    }
  },
  "sink": {
    "type": "DeltaSink",
    "storeSettings": {
      "type": "FabricLakeHouseWriteSettings",
      "path": "Tables/Sales"
    }
  }
}
```

**Data Flow**:
- **Transformations**: Filter, aggregate, join, derive columns.
- **Sink**: Write to OneLake, SQL, or Dataverse.

### **4. Synapse Data Engineering (Spark)**
**Notebooks**:
- **Languages**: Spark SQL, PySpark, Scala, R.
- **Magic Commands**:
  ```python
  %%spark
  df = spark.sql("SELECT * FROM Sales")
  %%pyspark
  df.show()
  %%sql
  SELECT COUNT(*) FROM Sales
  ```

**Spark Jobs**:
- **Submit Job**:
  ```python
  from pyspark.sql import SparkSession
  spark = SparkSession.builder.getOrCreate()
  df = spark.read.format("delta").load("Tables/Sales")
  df.write.format("delta").save("Tables/Sales_Processed")
  ```

**Delta Lake Commands**:
| Command | Example |
|---------|---------|
| Read Delta Table | `df = spark.read.format("delta").load("Tables/Sales")` |
| Write Delta Table | `df.write.format("delta").save("Tables/Sales")` |
| Update Table | `spark.sql("UPDATE Sales SET Amount = 100 WHERE ID = 1")` |
| Merge (Upsert) | `MERGE INTO Sales TARGET USING Updates SOURCE ON TARGET.ID = SOURCE.ID WHEN MATCHED THEN UPDATE SET *` |
| Time Travel | `spark.read.format("delta").option("versionAsOf", 0).load("Tables/Sales")` |
| Vacuum (Cleanup) | `spark.sql("VACUUM Sales RETAIN 168 HOURS")` |

**Optimizations**:
- **Z-Ordering**: Colocate data for faster queries.
  ```python
  spark.sql("OPTIMIZE Sales ZORDER BY (CustomerID, Date)")
  ```
- **Partitioning**: Split data by column (e.g., by year/month).
  ```python
  df.write.partitionBy("Year", "Month").format("delta").save("Tables/Sales")
  ```

---

### **5. Synapse Data Warehousing (T-SQL)**
**Key Features**:
- **T-SQL Endpoint**: Query lakehouse tables directly.
- **Materialized Views**: Pre-computed aggregations.
- **Stored Procedures**: Reusable T-SQL logic.

**Example Queries**:
```sql
-- Create table
CREATE TABLE Sales (
  ID INT,
  CustomerID INT,
  Amount DECIMAL(10,2),
  Date DATE
)
WITH (DISTRIBUTION = REPLICATE, CLUSTERED COLUMNSTORE INDEX);

-- Load data from OneLake
INSERT INTO Sales
SELECT * FROM OPENROWSET(
  'Delta',
  'https://onelake.dfs.fabric.microsoft.com/Workspace/Lakehouse/Tables/Sales'
);

-- Create materialized view
CREATE MATERIALIZED VIEW MV_SalesSummary AS
SELECT CustomerID, SUM(Amount) AS TotalSales
FROM Sales
GROUP BY CustomerID;

-- Query with T-SQL
SELECT * FROM Sales WHERE Date > '2023-01-01';
```

**Performance Tips**:
- Use **clustered columnstore indexes** for analytics.
- **Partition tables** by date/region.
- **Avoid `SELECT *`**: Specify columns.

---

### **6. Synapse Data Science**
**Key Tools**:
- **Notebooks**: Python/R with Spark.
- **ML Models**: Train/deploy with Azure ML integration.
- **Experiments**: Track metrics with MLflow.

**Example Workflow**:
```python
# Load data
df = spark.read.format("delta").load("Tables/Sales")

# Train model
from pyspark.ml.regression import LinearRegression
lr = LinearRegression(featuresCol="Features", labelCol="Amount")
model = lr.fit(df)

# Save model
model.write().overwrite().save("Models/SalesForecast")

# Log metrics to MLflow
from mlflow.tracking import MlflowClient
client = MlflowClient()
client.log_metric("rmse", 0.1)
```

**Azure ML Integration**:
- Register models in Azure ML.
- Deploy as endpoints for real-time scoring.


### **7. Synapse Real-Time Analytics (KQL)**
**Kusto Query Language (KQL)**:
- **Create KQL Database**:
  ```kql
  .create table StormEvents (StartTime:datetime, EventType:string, Damage:long)
  ```
- **Ingest Data**:
  ```kql
  .ingest inline into table StormEvents <|
  2023-01-01T00:00:00, "Tornado", 1000000
  2023-01-02T00:00:00, "Hurricane", 5000000
  ```
- **Query Data**:
  ```kql
  StormEvents
  | where EventType == "Tornado"
  | summarize TotalDamage=sum(Damage) by bin(StartTime, 1d)
  ```

**Event Streams**:
- **Capture**: Ingest from IoT, logs, or Kafka.
- **Process**: Use KQL for real-time aggregations.
- **Route**: Send to OneLake or Power BI.


### **8. Power BI Integration**
**Direct Lake Mode**:
- Connect Power BI directly to OneLake (no data import).
- **Steps**:
  1. In Power BI Desktop, select **Direct Lake**.
  2. Choose Fabric workspace > lakehouse/warehouse.
  3. Build reports on live data.

**Visualizations**:
- Use **Fabric-enabled visuals** (e.g., KQL queries in Power BI).
- **Real-Time Dashboards**: Stream data from KQL databases.

**Example**:
```powerquery
let
  Source = Fabric.DataLake("Workspace/Lakehouse/Tables/Sales")
in
  Source
```


### **9. Data Activator**
**Real-Time Triggers**:
- **No-Code Automation**: React to data changes (e.g., "Alert if sales > $1M").
- **Example Rule**:
  ```plaintext
  Trigger: WHEN Sales.Amount > 1000000
  Action: SEND EMAIL TO "manager@domain.com"
           AND POST MESSAGE TO TEAMS CHANNEL "#alerts"
  ```

**Supported Actions**:
- Send email/Teams message.
- Call Power Automate flow.
- Write to a table.


### **10. Security & Governance**
**Authentication**:
- **Azure AD**: Single sign-on (SSO).
- **Service Principals**: For automated workflows.

**Data Protection**:
- **Sensitivity Labels**: Classify data (e.g., "Confidential").
- **Row-Level Security (RLS)**:
  ```sql
  CREATE SECURITY POLICY SalesRLS
  ADD FILTER PREDICATE (CustomerID = USER_NAME());
  ```
- **Column-Level Security**: Mask sensitive data (e.g., SSN).

**Audit Logs**:
- Track access/changes in **Microsoft Purview**.


### **11. Performance Optimization**
**Lakehouse**:
- **Partitioning**: By date/region.
- **Z-Ordering**: For frequently filtered columns.
- **Compaction**: Merge small files.
  ```python
  spark.sql("OPTIMIZE Sales ZORDER BY (Date)")
  spark.sql("VACUUM Sales RETAIN 168 HOURS")
  ```

**Warehouse**:
- **Materialized Views**: Pre-aggregate data.
- **Indexing**: Clustered columnstore for analytics.

**Pipelines**:
- **Parallel Activities**: Run independent tasks concurrently.
- **Incremental Loads**: Process only new/changed data.


### **12. Collaboration & Deployment**
**Workspaces**:
- **Types**: Personal, Team, Organization.
- **Roles**: Admin, Member, Contributor, Viewer.

**Deployment Pipelines**:
1. **Develop**: Test in dev workspace.
2. **Deploy**: Move to test/prod via **Fabric Git Integration**.
3. **Monitor**: Use **Fabric Portal** for logs.

**CI/CD**:
- **Azure DevOps/GitHub**: Version control for notebooks/pipelines.
- **Fabric CLI**:
  ```bash
  fabric workspace deploy --name "Prod" --source "Dev"
  ```


### **13. Monitoring & Observability**
**Tools**:
- **Fabric Portal**: View pipeline runs, Spark jobs, queries.
- **Logs**:
  ```kql
  FabricLogs
  | where Operation == "NotebookExecution"
  | summarize count() by Status
  ```
- **Alerts**: Set up for failed jobs/long-running queries.

**Key Metrics**:
| Resource | Metrics to Monitor |
|----------|--------------------|
| **Spark Jobs** | Duration, memory usage, failures. |
| **SQL Queries** | Execution time, CPU usage. |
| **Pipelines** | Success rate, runtime. |
| **OneLake** | Storage used, read/write ops. |


### **14. Fabric CLI (Command Line)**
| Command | Purpose |
|---------|---------|
| `fabric workspace list` | List workspaces. |
| `fabric lakehouse create --name "Sales"` | Create lakehouse. |
| `fabric pipeline run --name "ETL"` | Trigger pipeline. |
| `fabric notebook execute --name "Transform"` | Run notebook. |
| `fabric sql query --warehouse "WH" --query "SELECT * FROM Sales"` | Run T-SQL. |


### **15. Common Use Cases**
| Scenario | Fabric Components | Key Steps |
|----------|-------------------|-----------|
| **Data Lakehouse** | OneLake, Spark, Power BI | Ingest → Transform (Spark) → Analyze (Power BI). |
| **Real-Time Analytics** | KQL DB, Event Streams | Ingest logs → Query with KQL → Trigger alerts. |
| **ML Model Training** | Data Science, OneLake | Load data → Train model → Deploy to Azure ML. |
| **ETL Pipeline** | Data Factory, Lakehouse | Extract (SQL) → Transform (Data Flow) → Load (OneLake). |
| **Data Warehousing** | Synapse SQL, Power BI | Create tables → Build aggregations → Visualize. |
| **IoT Processing** | Event Streams, KQL | Ingest sensor data → Detect anomalies → Alert. |


### **16. Troubleshooting**
| Issue | Solution |
|-------|----------|
| **Slow Queries** | Check partitioning, Z-ordering, or materialized views. |
| **Pipeline Failures** | Review logs in Fabric Portal; validate connections. |
| **Spark Job Errors** | Check driver/executor memory; optimize code. |
| **Permission Denied** | Verify Azure AD roles and OneLake ACLs. |
| **Data Not Refreshing** | Check shortcut paths; validate source access. |
| **Direct Lake Mode Issues** | Ensure Power BI has workspace permissions. |

**Debugging Tips**:
- Use `%%sql` in notebooks to validate queries.
- Test pipelines with small datasets first.
- Enable **diagnostic logs** in Fabric Portal.

### **17. Quick Reference: KQL vs. T-SQL vs. Spark SQL**
| Task | KQL (Real-Time Analytics) | T-SQL (Warehouse) | Spark SQL (Data Engineering) |
|------|---------------------------|------------------|-------------------------------|
| **Filter** | `where EventType == "Error"` | `WHERE EventType = 'Error'` | `WHERE EventType = 'Error'` |
| **Aggregate** | `summarize count() by Category` | `GROUP BY Category` | `GROUP BY Category` |
| **Join** | `join (Table1) on $left.ID == $right.ID` | `INNER JOIN Table1 ON Table1.ID = Table2.ID` | `JOIN Table1 ON Table1.ID = Table2.ID` |
| **Time Window** | `bin(Timestamp, 1h)` | `DATEPART(hour, Timestamp)` | `date_trunc('hour', Timestamp)` |
| **String Search** | `where Text contains "error"` | `WHERE Text LIKE '%error%'` | `WHERE Text LIKE '%error%'` |

### **18. Resources**
- **Docs**: [Microsoft Fabric Docs](https://learn.microsoft.com/en-us/fabric/)
- **Tutorials**: [Fabric Guided Tours](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)
- **Community**: [Fabric Tech Community](https://community.fabric.microsoft.com/)
- **Samples**: [Fabric GitHub](https://github.com/microsoft/fabric-samples)

**Pro Tip**: Use **Fabric’s "Try It Free"** workspace to experiment without committing to a license!
Need a deeper dive into any area (e.g., Delta Lake optimizations, KQL functions)?
