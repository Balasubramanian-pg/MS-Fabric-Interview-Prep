# Explain Ingestion Mapping In Kql

Canonical documentation for Explain Ingestion Mapping In Kql. This document defines concepts, terminology, and standard usage.

## Purpose
Ingestion mapping serves as the translation layer between raw, external data formats and the structured schema of a destination table. Its primary purpose is to decouple the physical structure of source data (such as JSON, CSV, or Avro) from the logical structure of the database. By defining these mappings, an engine can perform schema enforcement, data transformation, and field selection during the ingestion process, ensuring that data arrives in a query-ready state.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. While KQL is the primary interface for defining these mappings, the principles apply to any Kusto-based engine.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The logic of mapping source fields to destination columns.
* Supported mapping formats (tabular and multi-dimensional).
* Data type conversion and transformation during ingestion.
* The lifecycle of a mapping object (persistent vs. inline).

**Out of scope:**
* Specific cloud provider ingestion pipelines (e.g., Event Hubs, IoT Hub).
* Post-ingestion data transformation (Update Policies).
* Performance tuning for specific hardware configurations.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Ingestion Mapping** | A set of rules that instructs the engine how to map fields from a source file to columns in a target table. |
| **Source Path** | The specific location of a data element in the source file (e.g., a JSONPath or a CSV column index). |
| **Target Column** | The name of the column in the destination table where the data will be stored. |
| **Mapping Kind** | The format of the source data being mapped (e.g., `Json`, `Csv`, `Avro`, `Parquet`, `Orc`). |
| **Transformation** | A pre-defined function applied to source data during ingestion (e.g., `DateTimeFromUnixSeconds`). |
| **Persistent Mapping** | A mapping object stored in the database metadata and referenced by name. |
| **Inline Mapping** | A mapping definition provided as part of the ingestion command itself. |

## Core Concepts

### Schema Enforcement
Ingestion mapping acts as a gatekeeper. If a source field does not match the expected data type of the target column, the mapping logic determines whether to attempt a cast, nullify the value, or fail the record. This ensures that the destination table maintains a strict schema.

### Decoupling
By using mappings, the source data structure can change (e.g., adding new fields or reordering columns) without requiring a change to the destination table schema. Only the mapping definition needs to be updated to reflect the new source structure.

### Format-Specific Logic
*   **Tabular Mappings (CSV/TSV):** Rely on ordinal positions (indices).
*   **Structural Mappings (JSON/Avro):** Rely on property paths (JSONPath) to navigate nested hierarchies.
*   **Columnar Mappings (Parquet/ORC):** Map source column names to target column names.

## Standard Model
The standard model for ingestion mapping involves a collection of mapping entries. Each entry must contain:
1.  **Column:** The name of the destination table column.
2.  **Properties:** A property bag containing the source-specific location.
    *   For JSON: `{"Path": "$.property.subproperty"}`
    *   For CSV: `{"Ordinal": 0}`
    *   For Constant: `{"Value": "StaticValue"}`

### Mapping Object Structure
A mapping is typically represented as a JSON array of objects:
```json
[
  {"Column": "TargetColumnA", "Properties": {"Path": "$.SourceField1"}},
  {"Column": "TargetColumnB", "Properties": {"Ordinal": "2"}},
  {"Column": "TargetColumnC", "Properties": {"Transform": "DateTimeFromUnixMilliseconds", "Path": "$.Timestamp"}}
]
```

## Common Patterns

### Flattening
Extracting values from nested JSON objects or arrays and placing them into individual columns in a flat table.

### Constant Injection
Adding a column to the destination table that does not exist in the source data, populated by a static value defined in the mapping. This is often used for "SourceSystem" or "Environment" tags.

### Data Normalization
Using built-in transformations to convert source data into a standard format, such as converting Unix timestamps to UTC datetime objects or hex strings to integers.

## Anti-Patterns

### Over-Mapping
Attempting to perform complex business logic or multi-step transformations within the ingestion mapping. Ingestion mappings should be kept lightweight; complex logic should be handled via Update Policies or post-ingestion processing.

### Index-Based Mapping for Unstable Sources
Using ordinal (index-based) mapping for CSV files where the column order is not guaranteed. This leads to data corruption where values are placed in the wrong columns.

### Ignoring Data Types
Relying on the engine to "guess" the data type. Explicitly defining transformations ensures consistency and prevents ingestion failures when source data varies slightly.

## Edge Cases

### Missing Fields
If a mapping references a path or index that does not exist in a specific source record, the engine typically inserts a `null` value into the target column.

### Extra Fields
Fields present in the source data but not defined in the ingestion mapping are ignored. This allows for "sparse" ingestion from wide data sources.

### Dynamic Mapping
In scenarios where the schema is unknown or highly fluid, mapping the entire source record into a single column of type `dynamic` is a common fallback, though it sacrifices some query performance.

### Multi-line JSON
Ingestion mappings for JSON expect each record to be a single line (JSON Lines) or a specific array structure. Mapping a standard "pretty-printed" JSON file as a single stream requires specific configuration.

## Related Topics
*   **KQL Schema Management:** Defining the destination tables.
*   **Update Policies:** For cascading transformations after the initial mapping.
*   **Ingestion Properties:** Metadata governing the ingestion process (e.g., tags, creation time).
*   **Data Formats:** Detailed specifications for CSV, JSON, Parquet, and Avro.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |