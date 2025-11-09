Perfect. I’ll now create a **Microsoft Fabric 20-question multiple-choice mock exam** — same Markdown style, clean formatting, and factual explanations after each answer. It’ll match the tone and complexity of PL-300/DP-500 prep questions.

# **Microsoft Fabric – Practice Certification Test (20 Questions)**

---

### **Q1. Which component in Microsoft Fabric acts as the unified storage layer for all workloads?**

* A) Synapse Data Warehouse
* B) Azure Blob Storage
* C) **OneLake**
* D) Power BI Service

**✅ Correct Answer:** C) OneLake
**Explanation:** OneLake is Fabric’s unified data lake built on Delta format. It automatically underpins every workspace.

---

### **Q2. What format does Microsoft Fabric use to store data within OneLake?**

* A) JSON
* B) Avro
* C) **Delta (Parquet)**
* D) CSV

**✅ Correct Answer:** C) Delta (Parquet)
**Explanation:** Delta format adds ACID capabilities and supports schema evolution and time travel.

---

### **Q3. Which Fabric component is responsible for creating ETL/ELT data pipelines?**

* A) Synapse Data Engineering
* B) **Data Factory (Gen2)**
* C) Power BI
* D) Data Activator

**✅ Correct Answer:** B) Data Factory (Gen2)
**Explanation:** Data Factory is Fabric’s built-in data integration and pipeline orchestration tool.

---

### **Q4. What is the main advantage of Direct Lake Mode over DirectQuery?**

* A) Requires no licensing
* B) **Eliminates data refresh and improves performance**
* C) Requires manual refresh
* D) Uses an on-premises gateway

**✅ Correct Answer:** B)
**Explanation:** Direct Lake queries Delta tables in OneLake directly, combining freshness with import-level speed.

---

### **Q5. Which Fabric component provides Spark-based notebooks for transformations?**

* A) **Synapse Data Engineering**
* B) Power BI
* C) Data Activator
* D) Synapse Real-Time Analytics

**✅ Correct Answer:** A) Synapse Data Engineering
**Explanation:** It provides managed Spark environments for big-data transformations in Fabric.

---

### **Q6. The language used in Synapse Real-Time Analytics is:**

* A) SQL
* B) Python
* C) **Kusto Query Language (KQL)**
* D) DAX

**✅ Correct Answer:** C) KQL
**Explanation:** KQL is optimized for time-series, event, and telemetry analytics.

---

### **Q7. Which component triggers actions automatically based on data conditions?**

* A) Power Automate
* B) Synapse ML
* C) **Data Activator**
* D) Azure Logic Apps

**✅ Correct Answer:** C) Data Activator
**Explanation:** It detects patterns in real time and triggers alerts or workflows.

---

### **Q8. Fabric ensures security consistency across workloads using which service?**

* A) Azure Sentinel
* B) **Microsoft Purview**
* C) Defender for Endpoint
* D) Azure Policy

**✅ Correct Answer:** B) Microsoft Purview
**Explanation:** Purview provides unified governance, data lineage, and classification.

---

### **Q9. Shortcuts in OneLake allow you to:**

* A) Copy data between regions
* B) **Reference external data without duplication**
* C) Compress large datasets
* D) Delete data after ETL

**✅ Correct Answer:** B)
**Explanation:** Shortcuts virtualize external datasets from ADLS or S3 without physically copying them.

---

### **Q10. Which feature allows Power BI to use the same dataset across multiple reports?**

* A) Linked Reports
* B) **Semantic Models**
* C) Dataflows
* D) Shared Dashboards

**✅ Correct Answer:** B) Semantic Models
**Explanation:** Fabric’s semantic models define shared business logic reusable across reports.

---

### **Q11. Which Fabric service supports model training and experimentation?**

* A) Data Activator
* B) **Synapse Data Science**
* C) Power BI
* D) Synapse Real-Time Analytics

**✅ Correct Answer:** B) Synapse Data Science
**Explanation:** It provides notebooks, AutoML, and integration with Azure ML.

---

### **Q12. What’s the key difference between Fabric and Azure Synapse?**

* A) Fabric is PaaS; Synapse is SaaS
* B) **Fabric is SaaS; Synapse is PaaS**
* C) Fabric is only for Power BI
* D) Synapse supports Delta; Fabric doesn’t

**✅ Correct Answer:** B)
**Explanation:** Fabric is fully managed SaaS; Synapse requires infrastructure configuration (PaaS).

---

### **Q13. What is the storage cost implication of using Shortcuts vs ETL?**

* A) Shortcut is more expensive
* B) ETL is free
* C) **Shortcuts save cost by avoiding data duplication**
* D) Both cost the same

**✅ Correct Answer:** C)
**Explanation:** Virtual references mean less storage and reduced ETL overhead.

---

### **Q14. Which of the following languages is natively supported in Synapse Data Engineering?**

* A) T-SQL only
* B) **PySpark, Scala, R**
* C) C#
* D) JSONPath

**✅ Correct Answer:** B)
**Explanation:** Spark pools support PySpark, Scala, and R notebooks.

---

### **Q15. Which feature of OneLake enables direct analysis of data in external clouds like AWS S3?**

* A) Lakehouse ingestion
* B) **Shortcuts**
* C) Event streaming
* D) Delta duplication

**✅ Correct Answer:** B)
**Explanation:** Shortcuts let users link external cloud data as if it’s native to OneLake.

---

### **Q16. How does Fabric simplify collaboration between data engineers and analysts?**

* A) Shared Azure Resource Groups
* B) **Unified workspaces and semantic models**
* C) Separate databases
* D) Manual data export

**✅ Correct Answer:** B)
**Explanation:** Everyone works in the same workspace on the same OneLake-backed data.

---

### **Q17. What does the term “Direct Lake” specifically mean in Power BI?**

* A) A data import feature
* B) **A mode to directly query Delta tables in OneLake**
* C) A real-time warehouse connector
* D) A shortcut to S3 buckets

**✅ Correct Answer:** B)
**Explanation:** Direct Lake allows Power BI to read data directly from Delta tables without refresh.

---

### **Q18. Which Fabric concept ensures auditability and compliance across workloads?**

* A) Row-Level Security (RLS)
* B) **Data Lineage and Classification (Purview)**
* C) Role-based Access Control (RBAC)
* D) Defender Threat Rules

**✅ Correct Answer:** B)
**Explanation:** Purview tracks data lineage and classification across all Fabric assets.

---

### **Q19. What type of query engine does Synapse Data Warehousing use?**

* A) Rowstore engine
* B) **Massively Parallel Processing (MPP)**
* C) OLTP transaction engine
* D) Stream processor

**✅ Correct Answer:** B)
**Explanation:** Synapse Warehousing uses MPP architecture for high-volume analytical workloads.

---

### **Q20. What is the pricing model used in Microsoft Fabric?**

* A) Per storage only
* B) Per user
* C) **Capacity-based model (Capacity Units)**
* D) Per workspace

**✅ Correct Answer:** C)
**Explanation:** Fabric operates on a unified capacity model shared across workloads (measured in Capacity Units).

---

## **Scoring Key**

| Score Range | Level        | Interpretation                                             |
| ----------- | ------------ | ---------------------------------------------------------- |
| **17–20**   | Expert       | Ready for Fabric certification & project implementation    |
| **13–16**   | Proficient   | Understands Fabric concepts; needs more practical exposure |
| **9–12**    | Intermediate | Review OneLake, Direct Lake, and component integration     |
| **Below 8** | Beginner     | Revisit core Fabric architecture and workloads             |

---

## **Next Steps for Study**

> [!TIP]
>
> * Practice hands-on in Microsoft Learn Fabric environment.
> * Focus on **Direct Lake Mode**, **Shortcuts**, and **Data Factory Gen2 pipelines**.
> * Revise integration points with **Purview** and **Power BI**.
> * Read official Microsoft documentation: *aka.ms/fabric-docs*.

> [!NOTE]
> Expect 4–6 scenario questions in exams around workspace setup, access control, and governance using OneLake.

---

Would you like me to follow this up with a **visual Fabric architecture diagram (text-based Markdown flow)** showing how all eight components connect end-to-end in a data pipeline?
