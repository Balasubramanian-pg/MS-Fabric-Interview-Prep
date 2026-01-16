# What is the 'Real-Time Hub'?

In Microsoft Fabric, the **Real-Time Hub** is the "single source of truth" for all **data-in-motion**.

If OneLake is the central hub for static data (files and tables), the Real-Time Hub is the equivalent for streaming data. It is a tenant-wide, unified catalog where you can discover, manage, and act on streaming events from across your entire organization.

### What does it actually do?

The Real-Time Hub serves three primary functions:

1. **Discover (The Catalog):** It lists every data stream you have access to. This includes streams coming from Azure Event Hubs, IoT Hubs, and even **Fabric System Events** (like when a file is uploaded to OneLake or a Spark job finishes).
2. **Ingest (No-Code Connectors):** It provides a "Get Data" experience for streams. You can point it at external sources like Google Pub/Sub, Amazon Kinesis, or SQL Server (via Change Data Capture) and bring that data into Fabric without writing a single line of code.
3. **Act (Instant Response):** From the hub, you can directly trigger an **Activator** to send a Teams/Email alert if a certain condition is met, or route the stream into a **KQL Database** for immediate analysis.

### Key Components You’ll See in the Hub

| Component | What it is |
| --- | --- |
| **Data Streams** | Active flows of data currently running through your Fabric environment. |
| **Fabric Events** | System-level triggers, such as "Notebook Completed" or "Workspace Item Created." |
| **Microsoft Sources** | Native integrations with Azure services (Event Hubs, IoT Hubs, etc.). |
| **KQL Tables** | Real-time tables that are ready for sub-second querying using Kusto Query Language. |

### Why is it useful?

Before the Real-Time Hub, streaming data was often "siloed" inside specific applications or complex Azure setups. Now, a data analyst can browse the Hub, find a stream of "Live Factory Sensor Data" shared by an engineer, and immediately start building a real-time Power BI dashboard or an automated alert—all from one screen.

**Would you like to know how to set up an alert on a specific stream, or are you interested in how it connects to the KQL Database for fast querying?**
