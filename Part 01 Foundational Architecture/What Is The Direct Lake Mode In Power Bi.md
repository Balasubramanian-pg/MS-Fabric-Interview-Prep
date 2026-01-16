# What is the 'Direct Lake' mode in Power BI?

**Direct Lake** is a groundbreaking storage mode in Power BI (exclusive to Microsoft Fabric) that combines the performance of **Import mode** with the real-time nature of **DirectQuery**.

It essentially allows Power BI to "read" data directly from **Delta Lake** files in OneLake without needing to import them into a separate proprietary cache or translate them into SQL queries.

### How It Works: The "Transcoding" Magic

In traditional Power BI, you usually choose between two extremes:

1. **Import:** Blazing fast (in-memory), but you have to copy the data and schedule refreshes.
2. **DirectQuery:** Real-time, but slow because it sends SQL queries to the database every time a user clicks a visual.

**Direct Lake** breaks this trade-off by using **OneLake** as the single source of truth. Since Fabric stores data in Delta-Parquet (a columnar format), and Power BI’s engine (VertiPaq) is also columnar, Power BI can load those Parquet files directly into its memory on-demand.

### Key Advantages

* **No Data Movement:** You don't have to "refresh" data in the traditional sense. When the data in your Lakehouse/Warehouse changes, the report can see it almost instantly.
* **Massive Scale:** Because it only loads the specific columns needed for a visual into memory, it can handle multi-billion row datasets that would otherwise crash an Import model.
* **Near-Instant "Refresh":** Instead of a long ETL process, a "refresh" in Direct Lake is just a **"framing"** operation—it simply updates the metadata to point to the latest version of the files in the lake, which takes seconds.
* **No SQL Overhead:** Unlike DirectQuery, there is no "SQL translation" layer. The engine reads the files natively, resulting in performance that rivals Import mode.

### Comparison at a Glance

| Feature | Import Mode | DirectQuery | Direct Lake |
| --- | --- | --- | --- |
| **Performance** | Very Fast (In-memory) | Slower (Query-based) | **Fast (In-memory)** |
| **Data Freshness** | Scheduled Refresh | Real-time | **Near Real-time** |
| **Data Duplication** | Yes (Copy in PBI) | No | **No** |
| **Max Data Size** | Limited by Capacity | Unlimited | **Petabyte Scale** |

### Important Limitations

* **Fabric Only:** It only works if your data is in Microsoft Fabric (OneLake).
* **Fallback:** If a query becomes too complex or the capacity runs out of memory, it may "fallback" to **DirectQuery** mode, which will result in slower performance for that specific visual.
* **Modeling:** Certain features like Calculated Columns (DAX) or Calculated Tables are not supported; these must be handled upstream in the Lakehouse (using SQL or Spark).

### When should you use it?

You should use Direct Lake when you are working with **very large datasets** (Gold layer of a Medallion architecture) in Fabric and need **high performance** without the hassle of managing refresh schedules or the latency of DirectQuery.

[Microsoft Fabric: Direct Lake Mode in Power BI](https://www.youtube.com/watch?v=d61ndvqefzs)

This video provides a deep dive into how Direct Lake acts as a bridge between performance and real-time connectivity within the Fabric ecosystem.

**Would you like me to explain the "Fallback" mechanism in more detail, or perhaps help you decide if your current dataset is a good fit for Direct Lake?**
