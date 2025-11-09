# OneLake

*   OneLake is a single, unified, logical data lake for an entire organization. It is automatically provisioned with every Microsoft Fabric tenant, eliminating the need for any infrastructure management. This centralization of data storage aims to reduce data duplication and simplify data management across various analytical engines.
*   It is designed to be the central repository for all analytics data, analogous to how OneDrive functions for Office files. All Fabric data items, such as Lakehouses and Warehouses, automatically store their data in OneLake.
*   OneLake is built on top of Azure Data Lake Storage (ADLS) Gen2 and supports any type of file, whether structured or unstructured. This open-format approach ensures compatibility with a wide range of data processing tools and engines.

> [!NOTE]
> Every Microsoft Fabric tenant has exactly one OneLake. It's a core component that's always present and can't be removed.

## Core Components / Elements

*   **Tenant:** The highest level of organization in Microsoft Fabric, representing the entire company. Each tenant has a single OneLake instance.
*   **Workspaces:** These are collaborative environments within a tenant that allow different departments or teams to manage their data and analytics projects. Workspaces act as containers within OneLake, providing a way to distribute ownership and manage access policies.
*   **Fabric Items:** These are the various analytical assets that can be created within a workspace, such as Lakehouses, Warehouses, and KQL Databases. Data associated with these items is stored in OneLake.
    *   **Lakehouse:** A data architecture platform for storing, managing, and analyzing structured and unstructured data in a single location.
    *   **Warehouse:** A fully transactional data warehouse for structured data, supporting SQL-based analytics.
    *   **KQL Database:** A database optimized for querying large volumes of real-time, time-series data.
*   **Data:** The actual files and tables stored within the Fabric items. All tabular data in OneLake is stored in the Delta Parquet format.

### Storage: Delta Parquet Format

*   **Parquet:** An open-source, columnar storage file format that is highly efficient for analytics. It's designed to be compact and performant for read-heavy workloads.
*   **Delta Lake:** An open-source storage layer that brings ACID transactions, scalable metadata handling, and time travel (data versioning) capabilities to data lakes.
*   **Delta Parquet:** This is the format used by OneLake for all tabular data. It combines the efficiency of the Parquet file format with the reliability and performance features of Delta Lake. This format is open, allowing various compute engines to work with the data natively without needing to import or export it.

> [!IMPORTANT]
> The underlying storage for OneLake is Azure Data Lake Storage (ADLS) Gen2, but the data is organized and managed through the Delta Lake protocol.

### Hierarchy

*   The structure of OneLake is hierarchical, similar to a file system.
*   At the root is the OneLake for the tenant.
*   Workspaces appear as top-level folders within OneLake.
*   Within each workspace, Fabric items like Lakehouses, Warehouses, and KQL Databases are represented as subfolders.
*   Inside these item folders, you'll find further subdirectories for managed data (like `Tables`) and unmanaged data (`Files`).

A typical hierarchy looks like this:

```
/<Workspace>
  /<Lakehouse>
    /Tables
    /Files
  /<Warehouse>
  /<KQL Database>
```

*   This structure can be navigated using tools like the OneLake file explorer for Windows, which makes interacting with OneLake feel similar to using OneDrive.

### Access Methods

*   Data in OneLake can be accessed through various analytical engines and tools.
*   **T-SQL:** The SQL analytics endpoint of a Lakehouse or a Warehouse allows for querying data using Transact-SQL.
*   **Apache Spark:** Notebooks and Spark jobs can read and process data directly from OneLake, including from shortcuts.
*   **Power BI:** Power BI can connect to OneLake data in DirectLake mode, which provides high performance by loading data directly from the lake without needing to import it into a Power BI dataset.
*   **REST APIs:** OneLake supports the same APIs and SDKs as ADLS Gen2, making it compatible with existing applications and tools that can connect to ADLS Gen2.

> [!TIP]
> The OneLake file explorer for Windows is a convenient tool that allows you to browse and manage your OneLake files directly from your local machine, just like you would with any other file system.

### **Flashcards (Q&A)**

*   **Q: What is OneLake?**
    *   A: OneLake is a single, unified, logical data lake for an entire organization, automatically provisioned with every Microsoft Fabric tenant.
*   **Q: What is the underlying storage technology for OneLake?**
    *   A: OneLake is built on Azure Data Lake Storage (ADLS) Gen2.
*   **Q: What file format does OneLake use for tabular data?**
    *   A: OneLake stores all tabular data in the Delta Parquet format.
*   **Q: How is OneLake organized?**
    *   A: It has a hierarchical structure with workspaces as top-level folders, and Fabric items like Lakehouses and Warehouses as subfolders.
*   **Q: Can you have more than one OneLake per Fabric tenant?**
    *   A: No, every Fabric tenant has exactly one OneLake.
*   **Q: What are the primary ways to access data in OneLake?**
    *   A: Through T-SQL, Apache Spark, Power BI (in DirectLake mode), and REST APIs.
*   **Q: What is the benefit of the Delta Parquet format?**
    *   A: It combines the efficient columnar storage of Parquet with the reliability and transactional capabilities of Delta Lake, such as ACID transactions and time travel.
*   **Q: Can you interact with OneLake as if it were a regular file system?**
    *   A: Yes, using the OneLake file explorer for Windows, you can navigate and manage files in OneLake directly from your computer.
*   **Q: Are workspaces physical or logical containers in OneLake?**
    *   A: Workspaces are logical containers that help organize data and manage permissions within the single, unified OneLake.
*   **Q: Do you need to set up or manage any infrastructure for OneLake?**
    *   A: No, OneLake comes automatically with every Microsoft Fabric tenant with no infrastructure to manage.

## Shortcuts

*   Shortcuts in OneLake are objects that act as pointers to other storage locations, without moving or copying the data. They function like symbolic links in a file system.
*   The location a shortcut points to is called the **target path**, and where it appears is the **shortcut path**.
*   Shortcuts appear as folders within OneLake and can be used by any Fabric experience or analytical engine.

> [!NOTE]
> Deleting a shortcut does not affect the target data. However, if the target path is moved, renamed, or deleted, the shortcut will be broken.

### Create Shortcut

*   You can create shortcuts interactively through the Fabric UI or programmatically using REST APIs.
*   Shortcuts can be created in Lakehouses and KQL Databases.

#### Syntax Example (Conceptual - No Direct SQL DDL for External Shortcuts)

While there isn't a direct T-SQL `CREATE SHORTCUT` statement for external sources in the same way you might create other database objects, the creation process through the UI or APIs involves specifying the source and connection details.

Here's a conceptual representation of creating a shortcut to an ADLS Gen2 storage account:

```
// This is a conceptual representation. The actual creation is done via the Fabric UI or REST API.
CREATE SHORTCUT ADLSGen2Storage
WITH (
    STORAGEACCOUNT = 'yourstorageaccountname',
    CONTAINER = 'yourcontainername'
);
```

*   When creating a shortcut to an ADLS Gen2 account, you'll provide the URL, connection details (including authentication kind), and the path to the target folder or container.

### Shortcut Types

*   **Internal OneLake Shortcuts:** These point to other locations within OneLake, such as another workspace or a different Fabric item.
*   **External Shortcuts:** These point to data sources outside of OneLake. Supported external sources include:
    *   Azure Data Lake Storage (ADLS) Gen2
    *   Amazon S3
    *   Google Cloud Storage (GCS)
    *   Dataverse
    *   S3-compatible storage (including on-premises with an on-premises data gateway)

> [!WARNING]
> For ADLS Gen2 shortcuts, the storage account must have hierarchical namespaces enabled.

### Use Cases and Scenarios

*   **Unifying Data Across Clouds:** Create a single virtual data lake by connecting to data in Azure, AWS, and Google Cloud without moving or duplicating it.
*   **Cross-Domain Collaboration:** Allow teams to access and analyze data from other business domains without needing to copy it into their own workspace.
*   **Separation of Compute and Storage:** Keep raw data in one workspace (e.g., for data engineering on a processing capacity) and create shortcuts to it in another workspace for reporting and analysis on a separate capacity, which can improve performance and reduce resource conflicts.
*   **Reducing Data Duplication:** Eliminate the need for multiple copies of the same data, which lowers storage costs and reduces the complexity of ETL pipelines.

### Advanced Use Case: Secure Access to Private ADLS Gen2 with Trusted Workspace Access

*   You can create a OneLake shortcut to an ADLS Gen2 storage account that has public access disabled and is secured with a private endpoint.
*   This is achieved by using **Trusted Workspace Access**, which leverages the Fabric workspace's managed identity to authenticate.
*   You grant the workspace's service principal the necessary RBAC role (e.g., 'Storage Blob Data Reader') on the ADLS Gen2 container.
*   This allows Fabric to securely access the data without exposing the storage account to the public internet.

>

### **Flashcards (Q&A)**

*   **Q: What is a OneLake shortcut?**
    *   A: It's an object in OneLake that points to another storage location, either internal or external, without copying the data. It functions like a symbolic link.
*   **Q: What are the two main types of shortcuts?**
    *   A: Internal OneLake shortcuts (pointing to another location within OneLake) and external shortcuts (pointing to sources like ADLS Gen2, S3, or GCS).
*   **Q: What happens to the target data if you delete a shortcut?**
    *   A: Nothing. The target data remains unaffected.
*   **Q: What are some benefits of using shortcuts?**
    *   A: They help unify data across clouds, reduce data duplication, and allow for the separation of compute and storage.
*   **Q: In which Fabric items can you create shortcuts?**
    *   A: You can create shortcuts in Lakehouses and KQL Databases.
*   **Q: What is a prerequisite for creating a shortcut to an ADLS Gen2 storage account?**
    *   A: The storage account must have hierarchical namespaces enabled.
*   **Q: Can you create a shortcut to on-premises data?**
    *   A: Yes, for S3-compatible storage, you can use the Fabric on-premises data gateway (OPDG) to create a shortcut.
*   **Q: How does authentication for ADLS and S3 shortcuts work?**
    *   A: It's delegated using cloud connections. You create or select a connection that stores the necessary credentials.
*   **Q: Can shortcuts improve performance for Power BI?**
    *   A: Yes, by enabling Direct Lake mode on semantic models, which can read data directly from the shortcut without importation.
*   **Q: Can you create a shortcut inside another shortcut?**
    *   A: No, you cannot create shortcuts inside ADLS or S3 shortcuts.

## Permissions

*   OneLake security uses a layered approach, with permissions being managed at the workspace, item, and data (folder/table) levels. It employs a Role-Based Access Control (RBAC) model.
*   The security model is deny-by-default, meaning users have no access to data unless explicitly granted.

### Roles

#### Workspace Roles

*   Workspace roles are the first and broadest level of security, governing what users can do with all items within a workspace.
*   There are four primary workspace roles:
    *   **Admin:** Full control over the workspace, including managing permissions and deleting the workspace.
    *   **Member:** Can create and manage all items in the workspace and add other members or users with lower permissions.
    *   **Contributor:** Can create and edit items within the workspace but cannot manage workspace-level permissions.
    *   **Viewer:** Has read-only access to items in the workspace.

> [!IMPORTANT]
> Users with Admin, Member, or Contributor roles in a workspace generally have full read and write access to all data in OneLake for the items within that workspace. The more granular OneLake security roles primarily apply to users with the Viewer role.

#### OneLake Data Access Roles (Preview)

*   This is a more granular level of security that allows you to define roles that grant read access to specific folders or tables within a Fabric item.
*   These roles are created and managed at the item level (e.g., within a Lakehouse).
*   They are particularly useful for restricting data access for users who have the Viewer role at the workspace level.
*   You can also apply row-level security (RLS) and column-level security (CLS) to further refine access.

| Feature | Workspace Roles | OneLake Data Access Roles |
| :--- | :--- | :--- |
| **Scope** | Entire workspace and all its items | Specific folders and tables within an item |
| **Primary Users** | All users (Admin, Member, Contributor, Viewer) | Primarily affects users with the Viewer role |
| **Granularity**| Broad (e.g., read/write all data) | Fine-grained (e.g., read access to a specific table) |
| **Purpose** | Workspace and item management | Data-level security and access control |

### Grant Access

*   Managing access is done through assigning users or security groups to the appropriate workspace or OneLake security roles.

#### Granting Workspace Access

*   This is done through the "Manage access" settings of the workspace, where you can add users or groups to the Admin, Member, Contributor, or Viewer roles.

#### Granting Data Access with OneLake Security Roles

1.  Navigate to the Fabric item (e.g., a Lakehouse).
2.  Select "Manage OneLake security (preview)".
3.  Create a new role, giving it a name.
4.  Select the specific tables and/or folders that this role should have access to.
5.  Optionally, define RLS or CLS rules for tables.
6.  Add users or security groups as members of this role.

#### T-SQL for Permissions (in SQL Analytics Endpoint)

While OneLake security roles are defined through the Fabric UI, you can manage permissions within the SQL analytics endpoint of a Lakehouse or in a Warehouse using standard T-SQL commands. This is a separate layer of security that applies only to access through the SQL engine.

*   **Example of granting SELECT on a table to a user:**

```sql
GRANT SELECT ON Sales TO [user@domain.com];
```

*   This grants the specified user the ability to query the `Sales` table through the SQL endpoint.

### Best Practices

*   **Principle of Least Privilege:** Grant users the minimum level of access they need to perform their jobs. For read-only access to a subset of data, use the Viewer workspace role combined with specific OneLake security roles.
*   **Use Security Groups:** Manage permissions by adding security groups to roles rather than individual users. This simplifies administration as you can manage group membership in Microsoft Entra ID.
*   **Centralize Data and Federate Access:** Store data in a centralized workspace and use shortcuts to provide access to other teams in their own workspaces. This avoids data duplication while allowing for decentralized analysis.
*   **Understand Role Inheritance:** Be aware that permissions from workspace roles are inherited by the items within them. Admin, Member, and Contributor roles typically override more restrictive OneLake security settings.

### Common Pitfalls / Mistakes

*   **Over-assigning High-Privilege Roles:** Making too many users Admins or Members can expose all data within a workspace and bypass finer-grained security controls.
*   **Ignoring OneLake Security for Viewers:** Assuming that the Viewer role is sufficient to secure data. Without OneLake security roles, a Viewer might still have broader access than intended.
*   **Confusing SQL Permissions with OneLake Security:** T-SQL `GRANT`/`REVOKE` commands only apply to the SQL endpoint. A user might still be able to access the data through other means (like Spark or the OneLake API) if not restricted by OneLake security roles.
*   **Breaking Shortcut Permissions:** Not ensuring that users accessing data through a shortcut have the necessary permissions on the target data location.

### **Flashcards (Q&A)**

*   **Q: What are the four main workspace roles in Fabric?**
    *   A: Admin, Member, Contributor, and Viewer.
*   **Q: Which workspace roles are generally not affected by OneLake data access roles?**
    *   A: Admin, Member, and Contributor, as they typically have full read/write access to all data in the workspace's items.
*   **Q: What is the primary purpose of OneLake data access roles?**
    *   A: To provide granular, read-only access to specific folders and tables for users, particularly those with the Viewer workspace role.
*   **Q: What does the "deny-by-default" security model mean?**
    *   A: Users have no access to data unless it is explicitly granted to them through a role.
*   **Q: Can you apply row-level and column-level security in OneLake?**
    *   A: Yes, as part of defining OneLake data access roles for tables.
*   **Q: How should you manage permissions for large numbers of users?**
    *   A: By assigning security groups to roles instead of individual users.
*   **Q: Does granting `SELECT` on a table in the SQL endpoint also grant access via Spark?**
    *   A: No, SQL permissions are specific to the SQL engine. Access through other engines is controlled by workspace and OneLake security roles.
*   **Q: Where do you configure OneLake data access roles?**
    *   A: At the item level (e.g., in a Lakehouse) through the "Manage OneLake security (preview)" option.
*   **Q: What is the broadest level of security in OneLake?**
    *   A: Workspace roles, as they apply to all items within a workspace.
*   **Q: What is a common mistake regarding the Viewer role?**
    *   A: Assuming it automatically restricts access to a small subset of data. Without specific OneLake security roles, it may still allow access to all data in an item for reading.
