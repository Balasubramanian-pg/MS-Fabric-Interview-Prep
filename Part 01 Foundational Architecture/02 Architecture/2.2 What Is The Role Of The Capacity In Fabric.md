# What is the role of the 'Capacity' in Fabric?

In Microsoft Fabric, **Capacity** is essentially the "engine" that powers everything. If OneLake is the storage (like a hard drive), Capacity is the compute power (like the CPU and RAM) that allows you to actually do work with your data.

It is a dedicated pool of resources—measured in **Capacity Units (CUs)**—that is shared across all Fabric experiences, from running Spark notebooks to rendering Power BI reports.

### How Capacity Works

When you purchase a Fabric SKU (e.g., F2, F64, F2048), you are buying a specific amount of CU throughput. All workloads in your workspaces "draw" from this shared pool.

### Key Features of the Capacity Model

* **Universal Compute:** Unlike the old Azure model where you paid for SQL, Spark, and Power BI separately, Fabric uses one universal pool. If your Data Warehouse is idle, those CUs are automatically available for your Data Engineering jobs.
* **Bursting:** Fabric allows you to "over-speed" for short periods. If a job needs more power than your SKU normally provides, it can **burst** to use more CUs to finish quickly, provided there is overhead available.
* **Smoothing:** To prevent a single heavy job from crashing your system, Fabric "smooths" out the consumption.
* **Interactive jobs** (like a user clicking a report) are smoothed over 5 minutes.
* **Background jobs** (like a scheduled data refresh) are smoothed over 24 hours.


* **Throttling:** If you consistently use more than your purchased CUs, Fabric will throttle (slow down) your operations to protect the system's stability.

### Fabric SKUs and Scaling

Capacity is highly elastic. You can scale it up for a heavy month-end processing period and scale it back down afterward to save costs.

| SKU Level | Capacity Units (CU) | Equivalent Power BI Tier |
| --- | --- | --- |
| **F2 – F8** | 2 – 8 CUs | Entry-level / Dev testing |
| **F64** | 64 CUs | Equivalent to P1 (Premium) |
| **F2048** | 2048 CUs | Massive Enterprise scale |

> **Note:** Storage (OneLake) is billed separately from Capacity. You pay for the CUs you provision (Compute) and the Gigabytes you store (Storage).

[Microsoft Fabric & OneLake Explained in 2026](https://www.youtube.com/watch?v=RiRpt0o7rwA)

This video provides a clear walkthrough of the Fabric hierarchy, showing exactly how capacity sits between your tenant and your workspaces to power your data items.

**Would you like me to explain how to monitor your capacity usage using the Metrics App, or should we look at how to choose the right SKU size for your project?**
