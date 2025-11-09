# Microsoft Fabric Lakehouse

*   A Microsoft Fabric Lakehouse is a data architecture platform designed to store, manage, and analyze both structured and unstructured data in a single, centralized location. It combines the cost-effectiveness and flexibility of a data lake with the performance and structured features of a data warehouse.
*   At its core, the Lakehouse serves as a foundational element within the Microsoft Fabric ecosystem, built upon the unified OneLake storage layer. It enables various personas, from data engineers to data scientists and business analysts, to collaborate on the same data using their preferred tools without data duplication.
*   Every Lakehouse created in Fabric automatically provisions two key components: a SQL analytics endpoint for T-SQL based querying and a default semantic model for Power BI reporting, streamlining the journey from data ingestion to visualization.

> [!NOTE]
> The Lakehouse model standardizes on the open Delta Lake format for all tabular data, which adds a layer of reliability, performance, and ACID transaction capabilities on top of the underlying Parquet files stored in OneLake.

## Core Components / Elements

*   A Fabric Lakehouse is not a single monolithic tool but rather an item that brings together several powerful components, providing a unified experience for data professionals.

*   **### OneLake Storage (`Tables` and `Files`)**
    *   **Foundation:** Every Lakehouse stores its data in OneLake, Fabric's tenant-wide logical data lake.
    *   **Structure:** When a Lakehouse is created, two physical storage locations are automatically provisioned within its dedicated folder in OneLake:
        *   **`/Tables` (Managed Area):** This area is for storing managed Delta tables. When you save a Spark DataFrame as a table or use UI features like "Load to Table," the data is stored here in Delta format. Fabric automatically discovers and registers any Delta tables in this location, making them immediately queryable.
        *   **`/Files` (Unmanaged Area):** This area is for storing any type of raw data in any file format (e.g., CSV, JSON, images, logs). Data in this area is not automatically registered as a table, offering a landing zone for raw data before it is cleansed and structured into the `/Tables` area.

*   **### Delta Lake Tables**
    *   **Unified Format:** All tables within a Lakehouse are Delta tables. This open-source format enhances standard Parquet files with a file-based transaction log (`_delta_log`).
    *   **Key Features:** This format brings critical data warehousing features to the data lake, including:
        *   **ACID Transactions:** Ensures data integrity and reliability, even with concurrent operations.
        *   **Schema Enforcement & Evolution:** Prevents data corruption from incorrect data types and allows for schema changes over time.
        *   **Time Travel:** Enables querying of historical versions of a table for auditing, rollbacks, or reproducing experiments.

*   **### SQL Analytics Endpoint**
    *   **Automatic Provisioning:** For every Lakehouse, Fabric automatically creates a read-only SQL analytics endpoint.
    *   **Purpose:** This endpoint provides a familiar T-SQL interface for business analysts and SQL developers to query the Delta tables stored in the Lakehouse. It exposes the Delta tables as SQL tables, which can be queried using standard SQL tools and the TDS protocol.
    *   **Capabilities:** Users can perform read operations, create views, implement SQL object-level security, and connect to Power BI. However, it does not support data modification (DML) operations like `INSERT`, `UPDATE`, or `DELETE`.

*   **### Apache Spark Engine**
    *   **Primary Compute Engine:** The Apache Spark engine is the primary tool for data engineering and data science workloads within the Lakehouse.
    *   **Full Capabilities:** It provides full read and write capabilities, allowing users to ingest, transform, and cleanse data using Notebooks and Spark Job Definitions.
    *   **Language Support:** Developers can use multiple languages, including PySpark (Python), Spark SQL, Scala, and R, to interact with data.

### Comparison of Lakehouse Engines

| Feature | SQL Analytics Endpoint | Apache Spark Engine |
| :--- | :--- | :--- |
| **Primary Persona** | Business Analysts, BI Developers, SQL Professionals | Data Engineers, Data Scientists |
| **Primary Language** | T-SQL | PySpark, Spark SQL, Scala, R |
| **Data Operations** | Read-only (`SELECT`, `CREATE VIEW`) | Full CRUD (Create, Read, Update, Delete) |
| **Use Case** | Reporting, BI Dashboards, SQL-based exploration | ETL/ELT pipelines, Data Cleansing, Transformation, Machine Learning |
| **Execution Model** | Interactive, multi-client query processing | Batch and streaming processing |
| **Underlying Engine** | Polaris (Synapse SQL Engine) | Apache Spark |

### **Flashcards (Q&A)**

*   **Q: What are the two main storage areas automatically created in a Fabric Lakehouse?**
    *   A: The `/Tables` area for managed Delta tables and the `/Files` area for unmanaged raw files.
*   **Q: What is the standard format for all tables in a Fabric Lakehouse?**
    *   A: The Delta Lake format (Delta tables).
*   **Q: What is the SQL analytics endpoint in a Lakehouse?**
    *   A: It is an automatically generated, read-only endpoint that allows users to query Delta tables using T-SQL.
*   **Q: Can you perform `UPDATE` or `DELETE` operations on a Lakehouse table using the SQL analytics endpoint?**
    *   A: No, the SQL analytics endpoint is read-only. Data modification must be done using the Spark engine.
*   **Q: What is the primary compute engine for data engineering and transformation in a Lakehouse?**
    *   A: The Apache Spark engine.
*   **Q: What are the key benefits of using the Delta Lake format?**
    *   A: ACID transactions, schema enforcement and evolution, and time travel (data versioning).
*   **Q: Does data in the `/Files` section automatically appear as a queryable table?**
    *   A: No, data in the `/Files` section is considered unmanaged. To query it as a table, you must explicitly create a shortcut or an external table pointing to it.
*   **Q: How does a Lakehouse enable collaboration between different user personas?**
    *   A: By providing multiple engines (SQL and Spark) that can work on the same single copy of data in OneLake, allowing different teams to use their preferred tools without data duplication.

## Syntax & Parameters

*   Interacting with a Fabric Lakehouse involves two primary syntaxes: T-SQL for the SQL analytics endpoint and PySpark/Spark SQL for the Spark engine.

### **SQL Analytics Endpoint (T-SQL)**

*   The SQL analytics endpoint supports standard T-SQL `SELECT` syntax for querying data.

*   **Basic Query Syntax:**
    ```sql
    -- Query all columns from a table named 'Sales'
    SELECT *
    FROM Sales;

    -- Query specific columns with a filter
    SELECT
        OrderID,
        OrderDate,
        TotalAmount
    FROM
        Sales
    WHERE
        Region = 'North America';

    -- Create a SQL View for simplified access
    CREATE VIEW V_NorthAmerica_Sales AS
    SELECT
        OrderID,
        CustomerName,
        TotalAmount
    FROM
        Sales
    WHERE
        Region = 'North America';
    ```
*   **Parameters & Behavior:**
    *   **Read-Only:** Only `SELECT` statements and creation of read-only objects like views are permitted.
    *   **Three-Part Naming:** You can query across different Lakehouses or Warehouses within the same workspace using three-part naming conventions: `[lakehouse_name].[schema_name].[table_name]`.
    *   **Metadata Sync:** Metadata from the Delta Log is automatically read to keep the SQL schema up-to-date.

### **Spark Engine (PySpark & Spark SQL)**

*   The Spark engine offers a richer set of commands for data manipulation and transformation.

*   **Reading Data (PySpark):**
    ```python
    # Read a Delta table into a Spark DataFrame
    df_sales = spark.read.table("Sales")

    # Read a raw CSV file from the 'Files' section
    df_raw = spark.read.format("csv") \
                      .option("header", "true") \
                      .option("inferSchema", "true") \
                      .load("Files/raw_data/sales_q3.csv")

    df_sales.show(5)
    ```

*   **Writing and Transforming Data (PySpark):**
    ```python
    from pyspark.sql.functions import col, upper

    # Transform the data (e.g., convert region to uppercase)
    df_transformed = df_sales.withColumn("Region", upper(col("Region")))

    # Overwrite an existing Delta table (managed)
    df_transformed.write.mode("overwrite").saveAsTable("Sales_Transformed")

    # Save a DataFrame as a new Delta table in the managed area
    df_raw.write.mode("append").format("delta").saveAsTable("NewSalesData")
    ```
*   **Spark SQL Syntax:**
    *   You can execute Spark SQL directly in a notebook using the `%%sql` magic command.
    ```sql
    %%sql
    -- Create a new table from a CSV file
    CREATE TABLE regional_summary
    USING DELTA
    AS
    SELECT
      Region,
      COUNT(DISTINCT OrderID) AS OrderCount,
      SUM(TotalAmount) AS TotalRevenue
    FROM Sales
    GROUP BY Region;
    ```
*   **Key Parameters for `.write` method:**
    *   `.mode()`: Specifies the behavior if data already exists.
        *   `"overwrite"`: Replaces the entire table with the new data.
        *   `"append"`: Adds the new data to the existing table.
        *   `"ignore"`: Silently ignores the write operation if the table already exists.
        *   `"error"` or `"errorifexists"`: Throws an error if the table already exists (default).
    *   `.format()`: Specifies the file format. For all managed tables in a Lakehouse, this should be `"delta"`.
    *   `.saveAsTable()`: Saves the DataFrame as a managed table in the Lakehouse metastore, storing the data in the `/Tables` directory.
    *   `.partitionBy()`: Partitions the output data into subdirectories based on column values, which can significantly improve query performance.

## Use Cases and Scenarios

*   The Fabric Lakehouse is a versatile component designed to support a wide range of data-related workloads, from raw data ingestion to advanced analytics.

*   **### Unified Data Engineering (Medallion Architecture)**
    *   **Scenario:** An enterprise needs to build a scalable ETL/ELT pipeline to process raw data from various sources into a curated, analysis-ready format.
    *   **Implementation:** The Lakehouse is the ideal environment for implementing a medallion architecture.
        *   **Bronze Layer:** Raw, unstructured, or semi-structured data (e.g., JSON logs, CSV files) is ingested and landed in the `/Files` area of a "Bronze" Lakehouse.
        *   **Silver Layer:** Spark notebooks are used to read the bronze data, perform cleansing, validation, deduplication, and standardization, and save the results as structured Delta tables in a "Silver" Lakehouse.
        *   **Gold Layer:** Further business aggregations, feature engineering, and data modeling are performed on the silver data to create highly refined, project-specific Delta tables in a "Gold" Lakehouse or Warehouse, ready for BI and analytics.
    > [!TIP]
    > This layered approach promotes data reusability, improves data quality, and provides a clear lineage from raw data to business insights.

*   **### Data Science and Machine Learning**
    *   **Scenario:** A data science team wants to build a predictive model to forecast customer churn.
    *   **Implementation:**
        *   Data scientists can explore and prepare data directly from Gold or Silver layer tables in the Lakehouse using Spark notebooks.
        *   They can leverage integrated ML libraries (like Spark MLlib) or external ones (like scikit-learn) for model training.
        *   The Lakehouse's "Time Travel" feature is invaluable for reproducing experiments by accessing the exact version of the data used for training.
        *   Trained models can be registered with MLflow, which is natively integrated with Fabric, for tracking and deployment.

*   **### Real-Time Analytics and Streaming**
    *   **Scenario:** A retail company wants to analyze point-of-sale data in near real-time to monitor sales trends and manage inventory.
    *   **Implementation:**
        *   Fabric's Real-Time Intelligence features (like Eventstream) can capture streaming data from sources like Kafka or IoT Hub.
        *   This data can be directly streamed into a Delta table in the Lakehouse.
        *   Spark Structured Streaming can then be used to perform continuous transformations or aggregations on this data.
        *   A Power BI report connected in DirectLake mode to the Lakehouse table can visualize the live data with minimal latency.

*   **### Serving Layer for Business Intelligence**
    *   **Scenario:** A business intelligence team needs to create interactive dashboards for sales performance analysis.
    *   **Implementation:**
        *   The Gold Delta tables in the Lakehouse serve as the single source of truth.
        *   The automatically generated SQL analytics endpoint provides a direct, high-performance connection for Power BI.
        *   By setting the Power BI dataset to **DirectLake mode**, visualizations can query the data directly from OneLake without importing or duplicating it, offering the performance of in-memory models with the real-time nature of DirectQuery.

> [!CAUTION]
> While a Lakehouse is extremely flexible, for purely relational, high-concurrency BI workloads with a strong need for complex T-SQL procedures, a Fabric Data Warehouse may be a more optimized choice as the final serving layer. Often, the best architecture involves using both: a Lakehouse for ingestion and transformation (Bronze/Silver) and a Warehouse for the final serving layer (Gold).
