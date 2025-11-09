Alright — here’s the next advanced section of your handbook:
**“Fabric Cost Forecasting & Anomaly Detection Automation”** — written in the same Markdown documentation style so it fits right under your governance automation section.

---

# **Fabric Cost Forecasting & Anomaly Detection Automation**

*(Predictive Automation using Power BI + Power Automate + Azure ML)*

---

## **1️⃣ Purpose**

This automation layer predicts **cost overruns and anomalous CU behavior** before they occur.
It combines **Power BI forecasting**, **Azure Machine Learning endpoints**, and **Power Automate** flows to alert stakeholders when usage or cost patterns deviate significantly from historical norms.

> [!NOTE]
> This process doesn’t replace human review — it pre-empts capacity or cost issues and suggests corrective scaling or budget checks.

---

## **2️⃣ Architecture Overview**

| Layer             | Tool                      | Function                                               |
| ----------------- | ------------------------- | ------------------------------------------------------ |
| **Data**          | Fabric Metrics Dataset    | Provides hourly CU usage, cost, and workspace metadata |
| **Prediction**    | Azure ML Regression Model | Trains on historical CU and cost data                  |
| **Visualization** | Power BI Forecast visuals | In-dashboard cost trend prediction                     |
| **Automation**    | Power Automate            | Executes daily anomaly detection & alerting            |
| **Storage**       | OneLake                   | Stores ML outputs, alerts, and historical baselines    |

---

## **3️⃣ Data Preparation**

### **Required Tables**

| Table                | Columns                              | Source             |
| -------------------- | ------------------------------------ | ------------------ |
| `FactCapacityUsage`  | Date, WorkspaceID, UsedCU, CostINR   | Fabric Metrics App |
| `DimWorkspace`       | WorkspaceID, Department, Environment | OneLake            |
| `FactHistoricalCost` | Date, TotalCostINR                   | Fabric cost logs   |
| `FactBudget`         | Department, Month, BudgetINR         | Finance dataset    |

> [!TIP]
> Maintain **90 days of CU + cost history** minimum for training ML models.

---

## **4️⃣ Model Selection**

### **A. Power BI Forecast (Native)**

For quick forecasts:

* Visual: Line Chart → Enable “Forecast”
* Period: 14 days
* Confidence Interval: 80%
* Metric: `[Cost per Workspace (INR)]`

### **B. Azure ML (Custom Endpoint)**

For deeper predictions:

* Model Type: Multivariate Regression (e.g., ElasticNet)
* Features:

  * Avg CU Utilization (7-day rolling)
  * Idle Hours
  * Peak CU
  * Month
  * Department Budget
* Target: `CostINR` (daily)

> [!IMPORTANT]
> Deploy the model as an **Azure ML endpoint** and expose REST API for Power Automate.

---

## **5️⃣ Power Automate Flow – Forecast & Anomaly Detection**

### **Flow Overview**

| Step | Action                      | Description                 |
| ---- | --------------------------- | --------------------------- |
| 1    | Scheduled Trigger           | Daily, 7 AM IST             |
| 2    | Run Power BI Query          | Pull daily cost and CU data |
| 3    | Call Azure ML Endpoint      | Predict expected cost       |
| 4    | Compare Actual vs Predicted | Identify deviation          |
| 5    | Decision & Alert            | Notify on anomaly           |
| 6    | Log Results                 | Store in OneLake/SharePoint |

---

### **Step 1: Scheduled Trigger**

* Use “Recurrence” → every 24 hours.
* Store execution date as `varToday`.

---

### **Step 2: Fetch Current Data**

* Action: **Power BI – Run Query**
* Query:

  ```sql
  EVALUATE
  SUMMARIZECOLUMNS(
      'DimWorkspace'[Department],
      "ActualCostINR", [Cost per Workspace (INR)],
      "AvgCU", [CU Utilization %],
      "IdleHours", [Idle Hours]
  )
  ```
* Save as variable `varCurrentCostData`.

---

### **Step 3: Call Azure ML Endpoint**

* Add **HTTP Action**

  * **Method:** POST
  * **URL:** `https://<ml-endpoint>.azurewebsites.net/score`
  * **Headers:**

    ```
    Content-Type: application/json
    Authorization: Bearer <token>
    ```
  * **Body Template:**

    ```json
    {
      "data": "@{variables('varCurrentCostData')}"
    }
    ```
* Store response in `varPredictedCost`.

> [!TIP]
> Average the model output across departments to avoid single-department spikes skewing alerts.

---

### **Step 4: Compare Actual vs Predicted**

* Add **Compose** action:

  ```
  Deviation = ((ActualCostINR - PredictedCostINR) / PredictedCostINR) * 100
  ```
* Add **Condition:**

  ```
  IF abs(Deviation) > 15 THEN → Anomaly
  ELSE → Normal
  ```

---

### **Step 5: Send Alert**

#### Option A: Teams Channel

Use “Post adaptive card”:

```
⚠️ Fabric Cost Anomaly Detected  
Department: {{Department}}  
Predicted: ₹{{PredictedCostINR}}  
Actual: ₹{{ActualCostINR}}  
Deviation: {{Deviation}}%
```

Button: “View Dashboard” → link to Power BI report.

#### Option B: Email Summary

Send weekly digest with:

| Department | Actual | Predicted | % Deviation | Status |
| ---------- | ------ | --------- | ----------- | ------ |
| Finance    | ₹1.25L | ₹1.10L    | +13.6%      | OK     |
| Ops        | ₹2.85L | ₹2.20L    | +29.5%      | ALERT  |

---

### **Step 6: Log Forecasts**

* Create OneLake CSV log:

  ```
  Date,Department,PredictedCostINR,ActualCostINR,Deviation%,Status
  ```
* File path: `/Governance/ForecastLogs/Fabric_Cost_Predictions.csv`

> [!NOTE]
> Use Power BI Dataflow to import these logs and visualize long-term forecast accuracy.

---

## **6️⃣ Visualizing Forecasts in Power BI**

1. Create a **line chart**:

   * X-Axis: Date
   * Y-Axis: CostINR
   * Add Forecast: 14-day horizon, 80% confidence.
2. Overlay Azure ML predictions as a second line (via linked dataset).
3. Add a **scatter plot** comparing predicted vs actual (45° line).
4. Add KPI cards:

   * “Avg Forecast Deviation (%)”
   * “Current Month Overrun (₹)”
   * “Anomalies Detected (Count)”

> [!TIP]
> Use tooltip to show “Model Confidence” from Azure ML endpoint.

---

## **7️⃣ Threshold Management**

Store thresholds in OneLake `Settings_ForecastThresholds` table:

| Metric                | Value                                                                   | Notes                     |
| --------------------- | ----------------------------------------------------------------------- | ------------------------- |
| `MaxDeviation%`       | 15                                                                      | Flag anomalies above this |
| `ForecastHorizonDays` | 14                                                                      | Power BI forecast window  |
| `ConfidenceInterval`  | 0.8                                                                     | For predictive visuals    |
| `NotificationEmail`   | [finance-governance@company.com](mailto:finance-governance@company.com) | Alert recipients          |

Load this table at the start of every Power Automate run for dynamic control.

---

## **8️⃣ Maintenance Workflow**

| Task                         | Frequency | Owner             |
| ---------------------------- | --------- | ----------------- |
| ML Model retraining          | Quarterly | Data Science Team |
| Threshold review             | Monthly   | Finance Ops       |
| Alert log cleanup            | Monthly   | BI Admin          |
| Flow performance test        | Weekly    | Fabric Admin      |
| Power BI forecast validation | Monthly   | BI Lead           |

---

## **9️⃣ Benefits Summary**

✅ Early detection of abnormal cost spikes
✅ Predictive visibility into budget overruns
✅ Combined use of native BI + ML forecasting
✅ Automated, low-latency alerting
✅ Governance-friendly audit trail

> [!IMPORTANT]
> The forecasting system should be used **alongside human validation**.
> Automation aids awareness — final scaling or budget decisions stay with governance teams.

---

## **🔟 Storage & Documentation**

Save the full automation config under:
`/Governance/Automation/Fabric_Forecast_AnomalyGuide_v1.0.md`

Include supporting files:

* `/PowerAutomate/Flows/Fabric_ForecastAnomaly.zip`
* `/OneLake/ForecastLogs/`
* `/AzureML/Model_Deployment_Details.json`

---

Would you like me to add the **final extension** — “Fabric Governance Automation Suite Index,” a one-pager that ties all automations (alerts, scaling, forecasting, and dashboards) into a single control framework summary for internal presentation decks?
