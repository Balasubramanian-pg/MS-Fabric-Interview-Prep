# **Automated Scaling via Power Automate + Fabric REST API**

*(Instructional Addendum to the Developer Handbook)*

---

## **1️⃣ Purpose**

Automated scaling helps maintain **optimal CU performance** while minimizing unnecessary costs.
This section outlines how to integrate **Power Automate** with the **Fabric REST API** to automatically scale Fabric capacities **up or down** based on CU utilization thresholds detected in your dashboard dataset.

> [!NOTE]
> The automation doesn’t replace manual control — it’s a policy-based trigger system that nudges Fabric Admins with approval before scaling.

---

## **2️⃣ Prerequisites**

1. **Fabric Admin Role:** You must be a capacity admin in Microsoft Fabric.
2. **Service Principal / API Access:**

   * Register an app in Azure AD for API authentication.
   * Assign permissions to the Fabric API (`https://api.fabric.microsoft.com`).
3. **Environment Variables:**

   * `FabricCapacityID` (e.g., `F64-WEST-1`)
   * `FabricAPIBaseURL` = `https://api.fabric.microsoft.com/v1`
   * `AuthToken` (from Azure AD OAuth2 flow)
4. **Power BI Dataset:** Should expose `[CU Utilization %]` and `[Idle Hours]` measures.

---

## **3️⃣ Automation Flow Structure**

| Step | Action               | Description                                         |
| ---- | -------------------- | --------------------------------------------------- |
| 1    | **Trigger**          | Scheduled or refresh-complete from Power BI dataset |
| 2    | **Fetch Metrics**    | Get latest CU utilization value                     |
| 3    | **Decision Logic**   | Compare utilization vs defined thresholds           |
| 4    | **Approval**         | Notify Fabric Admin (Teams card)                    |
| 5    | **API Call**         | POST to Fabric scaling endpoint                     |
| 6    | **Confirmation Log** | Notify team and log to SharePoint / OneLake         |

---

## **4️⃣ Step-by-Step Setup**

### **Step 1: Create Flow**

1. Open **Power Automate → Create → Automated Cloud Flow**.
2. Name it `Fabric_AutoScale_Trigger`.
3. Choose trigger:

   * **Option A:** Power BI – “When a data-driven alert is triggered.”
   * **Option B:** Schedule – “Recurrence → Every 2 hours.”

---

### **Step 2: Retrieve CU Data**

1. Add action → **Power BI: Run a query against dataset**.
2. Choose workspace → Fabric Governance.
3. Enter query:

   ```sql
   EVALUATE
   SUMMARIZECOLUMNS(
       'Capacity'[CapacityName],
       "CU Utilization", [CU Utilization %]
   )
   ```
4. Save response as variable: `varCUUtilization`.

> [!TIP]
> Add rounding or average over last 2 hours for smoother decisions.

---

### **Step 3: Add Conditional Logic**

Add **Condition** block:

```
IF varCUUtilization > 85
    THEN → “Scale Up”
ELSE IF varCUUtilization < 45
    THEN → “Scale Down”
ELSE
    → “Do Nothing”
```

---

### **Step 4: Add Approval**

Insert **Teams Adaptive Card → Post and wait for response**.

**Message Example:**

```
⚙️ Fabric Auto-Scaling Request  
Capacity: F64-WEST-1  
Current CU Utilization: 92%  
Recommended Action: Scale Up to F128  
Approve or Reject
```

Buttons: Approve / Reject
Timeout: 30 minutes

---

### **Step 5: Scale API Call (on Approve)**

1. Add **HTTP** action.

2. Configure:

   * **Method:** POST
   * **URI:** `https://api.fabric.microsoft.com/v1/capacities/{FabricCapacityID}/scale`
   * **Headers:**

     ```
     Authorization: Bearer @{variables('AuthToken')}
     Content-Type: application/json
     ```
   * **Body:**

     ```json
     {
       "targetSku": "F128"
     }
     ```

3. Add **Condition** for “Scale Down” with `"targetSku": "F32"`.

> [!CAUTION]
> Scaling down reduces compute. Schedule this for off-peak hours to avoid query drops.

---

### **Step 6: Log Actions**

Add **SharePoint / OneLake file append** step:

* File: `/Governance/Logs/Scaling_Actions.csv`
* Fields:

  * DateTime
  * CapacityName
  * Action (Scale Up / Scale Down)
  * TriggerCU
  * ApprovedBy

> [!NOTE]
> Maintain this as a permanent audit log for governance reviews.

---

### **Step 7: Notifications**

Send **Teams message** → `#fabric-governance-alerts`:

```
✅ Capacity Scaling Executed  
Capacity: F64-WEST-1  
Action: Scale Up to F128  
Triggered by: Automated Threshold  
Approved by: @Admin
```

---

## **5️⃣ Recommended Thresholds**

| Environment        | Scale Up             | Scale Down          |
| ------------------ | -------------------- | ------------------- |
| **Production**     | > 85% CU for 30+ min | < 45% CU for 3+ hrs |
| **UAT / Dev**      | > 75% CU             | < 30% CU            |
| **Shared Sandbox** | > 60% CU             | < 25% CU            |

> [!TIP]
> You can store these thresholds in a `Settings` table in OneLake and read them dynamically instead of hardcoding.

---

## **6️⃣ Governance & Safety Controls**

* **Approval required** for all scale changes.
* **Only Fabric Admin** role can trigger HTTP POST.
* **Log retention:** 180 days minimum in SharePoint / OneLake.
* **Flow timeout:** 1 hour; auto-cancel if unapproved.
* **Test environment:** Always validate on a test capacity (e.g., F4) before production deployment.

---

## **7️⃣ Maintenance Checklist**

| Task                         | Frequency     | Owner        |
| ---------------------------- | ------------- | ------------ |
| Validate thresholds          | Monthly       | BI Lead      |
| Audit scaling logs           | Monthly       | Ops Lead     |
| Update API tokens            | Every 90 days | IT Admin     |
| Flow test run                | Bi-weekly     | Fabric Admin |
| Version backup (.zip export) | Monthly       | BI Developer |

> [!IMPORTANT]
> When scaling down, recheck pipeline and job concurrency settings — some Spark sessions may fail if resources shrink too fast.

---

## **8️⃣ Outcome Summary**

✅ Automated CU monitoring
✅ Admin approval workflow
✅ One-click API integration
✅ Audit log for every scale event
✅ Reduced manual overhead in Fabric capacity management

> [!FINAL NOTE]
> Store this automation design document under:
> `/Governance/Automation/Fabric_AutoScale_Guide_v1.0.md`
> and keep Power Automate export in `/Automation/Flows/Fabric_AutoScale_Flow.zip`.

---

Would you like me to write the **next section on “Fabric Cost Forecasting & Anomaly Detection Automation”** — how to automatically predict cost overruns using Power BI + Power Automate + Azure ML integration?
