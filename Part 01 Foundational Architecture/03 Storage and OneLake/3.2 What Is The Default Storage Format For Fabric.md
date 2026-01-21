# What is the default storage format for Fabric?

The default storage format for Microsoft Fabric is **Delta Lake** (often referred to as **Delta-Parquet**).

Fabric is built on an open, lake-centric architecture where all data is stored in **OneLake**. To ensure compatibility and high performance across different workloads (like Data Engineering, Data Warehouse, and Power BI), it uses Delta Lake as the unified standard.

### Key Characteristics of the Storage Format

* **Foundation:** It uses **Apache Parquet** as the underlying storage format, which is a columnar storage file type that provides excellent compression and efficient data retrieval.
* **ACID Compliance:** By using the **Delta** layer on top of Parquet, Fabric provides ACID (Atomicity, Consistency, Isolation, Durability) transactions, allowing for reliable data updates and deletes.
* **V-Order Optimization:** Microsoft applies a proprietary optimization called **V-Order** to these Delta files. This enhances "Direct Lake" performance in Power BI, allowing reports to read data directly from OneLake as quickly as if the data were imported into memory.
* **Open Standard:** Because it is based on the open-source Delta Lake standard, data stored in Fabric can be accessed by any other engine that supports Delta Lake, preventing vendor lock-in.

### Summary by Workload

While everything eventually lands in OneLake as Delta/Parquet, here is how different tools interact with it:

| Workload | Default Format Behavior |
| --- | --- |
| **Lakehouse** | Automatically saves tables as **Delta Lake** format. |
| **Data Warehouse** | Natively stores data in **Delta Lake** format (abstracted via SQL). |
| **Power BI** | Uses **Direct Lake** mode to read these Delta files directly without importing. |

Would you like to know more about how **V-Order** improves performance, or perhaps how to convert existing CSV/JSON files into this default format?
