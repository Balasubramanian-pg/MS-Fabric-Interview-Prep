# [Can You Use Dax In Fabric](Part 05 RealTime Science PowerBI/Can You Use Dax In Fabric.md)

Canonical documentation for [Can You Use Dax In Fabric](Part 05 RealTime Science PowerBI/Can You Use Dax In Fabric.md). This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of this topic is to define the role, availability, and architectural positioning of Data Analysis Expressions (DAX) within a unified data analytics ecosystem. DAX serves as the functional expression language designed for data modeling, analytical calculations, and business logic definition. In a modern data fabric, DAX bridges the gap between raw data storage and end-user consumption by providing a high-performance semantic layer.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the integration of DAX within the Fabric architecture.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The functional role of DAX within semantic models.
* The relationship between DAX and various storage modes (Import, DirectQuery, DirectLake).
* The theoretical boundaries of DAX as a calculation engine versus an ETL tool.
* Integration points between the semantic layer and the data lake.

**Out of scope:**
* Specific DAX function syntax or coding tutorials.
* Comparison with non-analytical languages (e.g., Python, Scala) for data engineering.
* Step-by-step UI instructions for specific software versions.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| DAX (Data Analysis Expressions) | A library of functions and operators that can be combined to build formulas and expressions for data modeling. |
| Semantic Model | A logical layer that sits between data sources and reporting tools, containing relationships, hierarchies, and DAX measures. |
| DirectLake | A storage mode that allows the DAX engine to load Parquet-formatted files directly from a data lake without translation to SQL or importing into a proprietary cache. |
| Evaluation Context | The environment in which a DAX formula is operated, consisting of Filter Context and Row Context. |
| Measure | A DAX calculation that is evaluated at query time based on the current user-defined filters. |
| Calculated Column | A DAX expression that is computed during data processing and stored within the model's table structure. |

## Core Concepts

### The Semantic Layer
In the Fabric architecture, DAX is the primary language for the semantic layer. While data may reside in a Lakehouse or Warehouse in Delta/Parquet format, DAX provides the business logic (e.g., Year-over-Year growth, Moving Averages) that transforms raw numbers into actionable insights.

### Engine Interaction
DAX operates within an in-memory analytical engine. In a unified fabric, this engine interacts with the storage layer in three primary ways:
1.  **Memory-Resident:** Data is compressed and loaded into memory for maximum DAX performance.
2.  **Direct Translation:** DAX queries are translated into native queries (like SQL) for the underlying data source.
3.  **Direct Metadata Access:** The engine reads industry-standard file formats (Delta/Parquet) directly, bypassing the need for a translation layer while maintaining in-memory performance.

### Functional vs. Procedural
DAX is a functional language. Unlike SQL (declarative) or Python (procedural), DAX is built on nested functions where the flow of data is determined by the evaluation context.

## Standard Model

The standard model for utilizing DAX within a data fabric is the **Star Schema**. 
*   **Fact Tables:** Contain the quantitative data (observations/events).
*   **Dimension Tables:** Contain the descriptive attributes (filters/groupings).

DAX is optimized for this structure. Using DAX on a single "flat" table or a complex "snowflake" schema often leads to performance degradation and increased complexity in formula logic.

## Common Patterns

### Time Intelligence
The most frequent application of DAX is for time-based calculations. This involves using a dedicated Date table to calculate metrics across different temporal granularities (MTD, QTD, YTD).

### Dynamic Aggregations
DAX is used to create measures that change their behavior based on the user's selection. This includes "Switch" patterns where a single visual can display different metrics (e.g., Revenue vs. Volume) based on a slicer.

### Row-Level Security (RLS)
DAX is the standard mechanism for defining security filters. By applying DAX expressions to tables, the system can restrict data visibility based on the identity of the user accessing the report.

## Anti-Patterns

### DAX as an ETL Tool
Using DAX (specifically Calculated Columns) to perform heavy data transformation or cleansing is discouraged. These operations should be "pushed upstream" to the data engineering layer (SQL or Spark) to ensure the semantic model remains lean and performant.

### Over-Complexity in Measures
Creating "God Measures"—single DAX expressions that attempt to handle dozens of disparate logic branches—leads to unmaintainable code. The standard approach is to use "Measure Branching," where complex logic is built upon simpler, atomic measures.

### Ignoring Filter Context
A common mistake is writing DAX that assumes a specific sort order or row position without explicitly defining the filter context, leading to unpredictable results when users interact with reports.

## Edge Cases

### DirectLake Fallback
In scenarios where the data volume exceeds memory limits or specific security features are used, the DAX engine may "fallback" from DirectLake mode to DirectQuery mode. This transition is transparent to the user but can significantly impact query performance.

### DAX in Paginated Reports
While DAX is primarily associated with interactive visuals, it can be used as a query language to retrieve flattened datasets for paginated, print-ready reports. In this case, DAX functions as a data retrieval language rather than just a calculation engine.

### Large String Manipulation
DAX is highly optimized for numeric aggregation but performs poorly with heavy string manipulation or "text mining" at scale. Such tasks are better suited for the data science or engineering layers of the fabric.

## Related Topics
*   **Star Schema Modeling:** The foundational data structure for DAX.
*   **VertiPaq Engine:** The underlying technology that executes DAX queries.
*   **Delta Lake Storage:** The physical storage format that enables DirectLake connectivity.
*   **Power BI Semantic Models:** The primary container for DAX logic.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |