# Benchmarking Framework: Microsoft Fabric vs Snowflake vs Databricks vs Azure Synapse

*A practitioner’s playbook for cost, performance, and governance comparisons*

## 1. Purpose and Outcomes

* Establish a reproducible, apples-to-apples framework to benchmark **cost**, **performance**, **scalability**, and **operational maturity** across four platforms:

  * Microsoft Fabric
  * Snowflake
  * Databricks (Lakehouse)
  * Azure Synapse Analytics
* Produce defensible results for **platform selection**, **capacity sizing**, and **TCO modeling**.
* Deliverables

  * Benchmark runbook and automation scripts
  * Metrics dataset with raw runs and normalized scores
  * Executive scorecard with weighted decision model
  * Cost forecast sheets by workload and growth scenarios

## 2. Benchmark Principles

* **Fairness**: Same data volume, schema, and query logic where semantics allow; platform-native best practices applied evenly.
* **Reproducibility**: Versioned code, containerized tools, fixed seeds, and published configs.
* **Representativeness**: Include OLAP, ELT, streaming, ML, concurrency and BI workloads.
* **Statistical rigor**: Multiple runs, outlier handling, confidence intervals, and effect sizes.
* **Cost realism**: Use platform-native billing telemetry and include storage, compute, and orchestration where applicable.

## 3. Scope and Workload Suite

### 3.1 Canonical Workload Families

* **ELT/ETL**: Ingest → transform (star schema build, CDC apply, SCD Type-2).
* **SQL analytics**: Star-join queries, window functions, distinct count, semi-joins, nested subqueries.
* **Concurrency**: Mix of short and long queries with 16–128 parallel users.
* **Large scan + selective filter**: Column pruning and partition pruning tests.
* **Streaming / real-time**: 5–50K events/sec ingest; 1–5 sec end-to-end latency target.
* **ML/DE**: Spark-based feature engineering + model training (XGBoost/LightGBM or PySpark ML).
* **BI/model serving**: Semantic model refreshes, Direct/Import/Direct Lake query latency.

### 3.2 Data Generators and Datasets

* **Synthetic**: TPC-DS-like star schema at 1 TB and 10 TB scales; event stream generator (Kafka-style) with configurable skew.
* **Realistic**: Public retail/finance datasets (if licensing permits) normalized to Delta/Parquet.
* **Skew scenarios**: Zipfian keys to stress joins and shuffles.
* **Compression**: Parquet (Snappy) or Delta with ZSTD; identical file counts and target row group sizes.

### 3.3 Test Matrix (example)

| Workload            |  Scale | Users | Run Count | Success Criteria              |
| ------------------- | -----: | ----: | --------: | ----------------------------- |
| ELT build (SCD2)    |   1 TB |     1 |         5 | < N minutes, no data mismatch |
| Star-join analytics |   1 TB |    32 |        10 | p95 latency < threshold       |
| Concurrency mix     |   1 TB |    64 |        10 | pass rate ≥ 99%               |
| Streaming 10K eps   |    n/a |   n/a |         3 | end-to-end < 5s p95           |
| ML training         |   1 TB |     1 |         5 | same AUC within tolerance     |
| BI semantic refresh | 500 GB |     1 |         5 | refresh time < target         |

## 4. Normalization Rules

* **Schema**: Identical logical schema; platform-specific DDL only for storage/index/format hints.
* **File layout**: Same number of files and average file size per partition after load.
* **Statistics**: Auto-stats or ANALYZE TABLE executed as per platform best practice.
* **Caching**: Warm and cold runs recorded separately.
* **Optimizations**: Apply native, equivalent features:

  * Fabric/Databricks Delta OPTIMIZE + ZORDER vs Snowflake clustering vs Synapse partition + statistics
* **Retries/failures**: Counted and logged; runs with transient faults may be retried once and flagged.

## 5. Metrics and KPIs

### 5.1 Performance

* **Latency**: p50, p90, p95 for each query group and workload
* **Throughput**: rows/sec or MB/sec for ETL/stream
* **Concurrency success**: % completed within SLA
* **Refresh time**: BI model refresh/import/Direct Lake materialization
* **Shuffle and spill**: spill bytes, shuffle reads/writes (Spark-based engines)

### 5.2 Cost

* **Compute cost**: capacity CUs (Fabric), credits (Snowflake), DBUs/VTUs (Databricks/Synapse) consumed per workload
* **Storage cost**: per TB-month; include delta change logs and time travel/retention
* **Orchestration cost**: pipeline/Jobs/Tasks where billed
* **Cost per outcome**: cost per 1M rows transformed, per query, per user, per GB scanned
* **Idle cost**: reserved capacity burn during idle windows (where applicable)

### 5.3 Reliability and Ops

* **Failure rate**: per workload and per hour
* **Elasticity time**: scale-up/scale-down time to effect
* **Cold start time**: cluster/warehouse ready time
* **SLA adherence**: % runs meeting declared SLOs
* **Governance signals**: lineage completeness, access policy coverage (binary/percent)

### 5.4 User Experience (BI)

* **Visual interaction**: p95 visual query time with slicers
* **Semantic model**: memory footprint, refresh duration
* **Concurrency at BI layer**: 50–200 users hitting same model

## 6. Environment Setup (per Platform)

> Use IaC where possible; record SKU/edition/regions.

### 6.1 Microsoft Fabric

* **Capacity**: Select F-SKU (e.g., F64). Record region, auto-pause settings for non-prod.
* **OneLake**: Create domains; stage Delta tables with shortcuts if reusing external lake.
* **Workloads**: Enable Data Engineering, Data Warehouse, Real-Time Analytics, Power BI.
* **Optimizations**: OPTIMIZE and Z-order large Delta tables; Direct Lake for BI where feasible.
* **Telemetry**: Fabric Capacity Metrics App; Power BI audit logs exported to OneLake.

### 6.2 Snowflake

* **Warehouses**: Create XS–XL as needed; auto-suspend/auto-resume set to realistic values.
* **Storage**: External vs internal; stage Parquet in external stage if comparing external tables.
* **Optimizations**: Clustering keys for large fact tables; RESULT_SCAN behavior noted; query acceleration service optional and flagged.
* **Telemetry**: ACCOUNT_USAGE views; Resource Monitor for credit caps.

### 6.3 Databricks

* **Clusters/SQL Warehouses**: Serverless SQL where allowed; otherwise pro or classic; photon on.
* **Unity Catalog**: Enable; register Delta tables; enforce access policies.
* **Optimizations**: Delta OPTIMIZE + ZORDER; AQE; autotune shuffle partitions.
* **Telemetry**: Ganglia/metrics, system tables, cost via billing export (DBU mapping).

### 6.4 Azure Synapse

* **Dedicated SQL pools**: DWU sizing and scaling rules
* **Serverless SQL**: External tables or OPENROWSET; set file formats
* **Spark pools**: Node counts, autoscale; library management
* **Optimizations**: CTAS to distribute data; statistics updates
* **Telemetry**: Azure Monitor, sys.dm_pdw_* views, Log Analytics

## 7. Execution Orchestration

* **Controller**: Python runner or Airflow/ADF orchestrating equivalent steps across platforms.
* **Phases per workload**:

  1. Load data
  2. Prepare stats/optimize
  3. Warm run (optional)
  4. Cold runs N times
  5. Concurrency runs
  6. Collect logs/metrics
* **Seeding**: Fixed random seeds for data generation and ML.
* **Timeboxing**: Abort long-tail runs after configurable timeout; mark as failure.

## 8. Query and Transformation Specs

* **SQL portability**: Maintain a canonical SQL, then platform-specific translations:

  * DATE/TIMESTAMP functions, VARIANT/STRUCT handling, semi-structured JSON reads
  * Window functions: ROW_NUMBER, SUM OVER PARTITION ORDER
  * Bloom filters / data skipping hints when available
* **ELT patterns**:

  * SCD2 merge: canonical MERGE with effective/expiry timestamps
  * CDC apply: upserts with delete markers
  * Aggregation layers: snapshot vs rolling windows
* **Spark notebooks**:

  * PySpark transformations with checkpointing
  * Autotune partitions; cache only when justified by reuse

## 9. Concurrency Design

* **Mixed workload script**: 70% short analytical queries, 20% medium joins, 10% heavy aggregations.
* **Arrival process**: Poisson arrivals with λ configurable to reach target concurrency (16–128).
* **Back-pressure**: Record queueing delays and “resource busy” responses.
* **Isolation**: Dedicated test window with no extraneous activity.

## 10. Streaming Tests

* **Ingest**: Simulated Kafka producers at 5K, 10K, 25K events/sec.
* **Transform**: Sliding/tumbling windows; dedup; enrichment via dimension cache.
* **Serve**: Materialized view or KQL/Delta live table.
* **KPIs**: end-to-end p95 latency, late-data handling, exactly-once semantics, backlog recovery time.

## 11. ML/DE Tests

* **Feature build**: joins + windowed features over 100M rows.
* **Training**: XGBoost/LightGBM on 100M rows; AUC and training time.
* **Hyper-param search**: small grid or Bayesian sample; cap total trials.
* **Parity**: Same random seed, same train/test split; similar library versions where possible.

## 12. BI Tests

* **Model types**: Import vs Direct (Snowflake/Databricks/Synapse SQL) vs Direct Lake (Fabric).
* **Refresh**: Full and incremental refresh paths measured.
* **Visuals**: 10-card page with two slicers; record first-render and cross-filter latency.
* **Concurrency**: 50–200 simulated users via capacity/load test tools.

## 13. Cost Measurement

* **Compute**:

  * Fabric: CU consumption per window via Metrics App; record capacity SKU and duration.
  * Snowflake: Credits consumed per warehouse and per query (QUERY_HISTORY,WAREHOUSE_METERING).
  * Databricks: DBUs from billing export mapped to jobs/warehouses.
  * Synapse: DWU hours (dedicated), per-TB scan (serverless), Spark node hours.
* **Storage**: TB-month for base + change logs + time travel/retention set identically.
* **Orchestration**: Pipeline/Jobs/Task charges if applicable.
* **Normalization**: Cost per workload unit (e.g., ₹ per 1M rows transformed; ₹ per 100 queries at p95 target).

## 14. Telemetry and Logging

* **Standard log schema** (stored in Delta/Parquet):

  * run_id, platform, workload, query_id, attempt, start_ts, end_ts, duration_ms
  * cold_warm_flag, scale_sku, concurrency_level
  * bytes_scanned, rows_output, shuffle_spill_bytes
  * success_flag, error_code, error_message
  * cost_units (CU/credit/DBU/DWU), cost_in_inr
* **Collectors**:

  * SQL: query_history views per platform
  * Spark: job metrics APIs
  * BI: Power BI audit logs (for Fabric), tool logs for others
* **Time sync**: NTP synchronized; all logs in UTC with local offset column.

## 15. Statistical Methods

* **Multiple runs**: N ≥ 5 per case; report median and p95 with 95% CI (bootstrap).
* **Outliers**: Winsorize at 5% if required; also report raw.
* **Effect sizes**: Ratio of medians with CI; annotate “material difference” threshold (e.g., ±15%).
* **Stability**: Coefficient of variation per test; flag unstable cases.

## 16. Scoring and Decision Model

### 16.1 Criteria and Weights (example)

| Category              | Weight | Sub-metrics                                    |
| --------------------- | -----: | ---------------------------------------------- |
| Performance           |    35% | Latency p95, throughput, concurrency success   |
| Cost                  |    35% | Compute cost/unit, idle cost, storage overhead |
| Operations            |    15% | Elasticity, cold start, failure rate           |
| Governance & Security |    10% | Lineage, policy coverage, auditability         |
| BI Experience         |     5% | Refresh time, visual latency                   |

### 16.2 Scoring Method

* Normalize each metric to 0–100 (higher better).
* Weighted sum for category scores; overall score is weighted sum across categories.
* Provide **sensitivity analysis** for alternate weight sets (finance-heavy vs performance-heavy).

## 17. Governance and Compliance Checks

* **Lineage**: Purview/Unity Catalog/Information Schema coverage; % entities with lineage.
* **Access**: RLS/OLS/Column masking equivalents; test unauthorized access attempts.
* **Audit**: Availability of immutable logs for admin and data access.
* **Data residency**: Region controls and cross-region egress visibility.
* **PII handling**: Tokenization/masking and KMS integration.

## 18. Runbook Outline (per workload)

1. **Prepare**: Create schema; load data; execute stats/optimize.
2. **Validate**: Row counts, hash totals, checksum sample values.
3. **Warm run**: One execution, discard metrics.
4. **Cold runs**: N executions; log metrics.
5. **Concurrency**: Launch harness; hold for target duration.
6. **Collect**: Export query/job histories and cost.
7. **Analyze**: Compute KPIs, aggregates, CI.
8. **Report**: Publish charts and decision tables.

## 19. Automation Hints (pseudo)

* **Controller** (Python):

  * `platform` abstraction with `load_data()`, `optimize()`, `run_query_set()`, `collect_metrics()`.
  * YAML config describing SKUs, credentials, datasets, and concurrency plans.
* **Schedulers**:

  * Fabric: Pipelines/Data Factory; Power Automate for alerts
  * Snowflake: Tasks; query tags for grouping cost
  * Databricks: Jobs with job clusters/warehouses
  * Synapse: Pipelines/Triggers
* **Artifacts**: Store all outputs in `/benchmarks/{platform}/{date}/`.

## 20. Reporting Templates

### 20.1 Executive Summary (one-pager)

* Top-line: **Cost per workload unit** and **p95 latency** by platform.
* Heatmap: Green/Amber/Red per category.
* Recommendation: Platform by workload fit, not one-size-fits-all.

### 20.2 Detailed Tabs

* **Performance**: Boxplots per workload; concurrency curves.
* **Cost**: Stacked bars of compute/storage/orchestration; idle vs active.
* **Ops**: Elasticity timelines; cold start distributions.
* **BI**: Refresh/interaction timings; concurrency success.

## 21. Interpreting Results (Patterns to Expect)

* **Fabric**: Strong BI coupling; Direct Lake often wins on BI latency and refresh avoidance. Concurrency elastic via capacity; watch CU planning and idle cost.
* **Snowflake**: Excellent SQL performance and concurrency; predictable auto-suspend; credits transparency; consider cost of frequent small resumes.
* **Databricks**: Strong Spark/Delta performance, ML pipelines; Photon boosts SQL; ensure SQL warehouse sizing and catalog governance are tuned.
* **Synapse**: Dedicated SQL pools can excel in predictable warehouse patterns; serverless convenient for ad hoc; Spark parity depends on pool tuning.

## 22. Risks and Mitigations

| Risk                               | Impact                     | Mitigation                                      |
| ---------------------------------- | -------------------------- | ----------------------------------------------- |
| Non-equivalent SQL semantics       | Biased or failing tests    | Port queries carefully; validate outputs        |
| Hidden caches skewing              | Unrealistic warm results   | Separate cold vs warm explicitly                |
| Credit/CU leakage during idle      | Inflated costs             | Enforce auto-suspend/auto-pause windows         |
| Networking/egress                  | Latency and surprise costs | Co-locate storage/compute; record regions       |
| Over-optimization for one platform | Unfair comparison          | Apply native best practices evenly and document |

## 23. Acceptance Criteria

* All workloads executed with ≥ 5 valid cold runs.
* Data correctness checks pass (row counts, aggregates, hash totals).
* Confidence intervals computed; no single-run claims.
* Cost captured from native billing sources.
* Reproducibility: configs and code in version control with commit hash.

## 24. Handover Package Structure

```
/benchmark/
  /configs/
    fabric.yaml
    snowflake.yaml
    databricks.yaml
    synapse.yaml
  /workloads/
    elt/
    sql/
    concurrency/
    streaming/
    ml/
    bi/
  /orchestration/
    runner.py
    harness/
  /artifacts/
    {platform}/{date}/logs.parquet
    {platform}/{date}/cost.csv
  /analysis/
    metrics_agg.parquet
    scorecards.xlsx
  /reports/
    executive_summary.pptx
    technical_appendix.md
```

## 25. Example Canonical SQL (portable core)

```sql
-- Star-join analytics: revenue by customer segment last 90 days
SELECT
  d.calendar_month,
  c.segment,
  SUM(f.net_amount) AS revenue,
  COUNT(DISTINCT f.order_id) AS orders
FROM fact_sales f
JOIN dim_date d    ON f.date_key = d.date_key
JOIN dim_customer c ON f.customer_key = c.customer_key
WHERE d.date >= DATEADD(day, -90, CURRENT_DATE)
GROUP BY d.calendar_month, c.segment
ORDER BY d.calendar_month, revenue DESC;
```

* Translate platform-specific bits:

  * Date arithmetic, identifiers quoting, and materialized views vs result caching.

## 26. BI Test Script (outline)

* Build semantic model with:

  * 1 large fact (≥ 200M rows), 6 dims
  * 10 measures (SUM, DISTINCTCOUNT, YTD, rolling 90-day)
* Pages:

  * Summary cards, trend line, top-N table, slicers on date/segment/region
* Tests:

  * First load cold cache
  * Cross-filter p95 across 10 interactions
  * Refresh path (full vs incremental)

## 27. Cost Models and Forecasting

* **Bottom-up**:

  * Compute per workload × expected daily frequency × growth
  * Storage growth with retention policies
* **Scenarios**:

  * Conservative: 5% monthly query growth
  * Base: 10% growth, modest streaming
  * Aggressive: 20% growth + ML at scale
* **Outputs**:

  * 12- to 36-month cost curve per platform
  * Break-even points for capacity upgrades or warehouse sizes

## 28. Decision Playbook

1. **By workload fit**:

   * BI-centric with tight Power BI integration → Fabric advantages
   * Pure SQL analytics at high concurrency → Snowflake strong
   * Unified DE/ML + SQL with open formats → Databricks strong
   * Established EDW patterns and ADF tie-ins → Synapse viable
2. **By operating model**:

   * Central capacity with cost governance → Fabric
   * Departmental warehouse autonomy → Snowflake multi-warehouse
   * Data science first → Databricks
   * Azure PaaS alignment → Synapse
3. **By constraints**:

   * Strict residency/sovereignty → compare regional SKUs/features
   * Tooling lock-in or staff skills → weight training costs

## 29. Checklist (Quick Audit)

* Data parity (schema, file layout) verified
* Best practices applied on all platforms and documented
* Cold vs warm runs isolated and labelled
* Concurrency harness verified
* Cost captured from native sources
* Statistical summary produced with CI
* Scorecard generated with transparent weights
* Executive deck prepared with recommendations

## 30. Next Steps

* Dry run the framework at **1 TB scale** to validate scripts and telemetry.
* Tune obvious misconfigurations discovered in dry run; re-baseline.
* Execute full matrix (including 10 TB if relevant).
* Present results with sensitivity analysis and scenario costs.
* Decide on **platform by workload** and plan phased adoption.

---

### Appendix A: Platform-Specific Hints

* **Fabric**

  * Use **Direct Lake** for BI to skip import overhead when feasible.
  * Watch **CU idle cost**; schedule auto-pause for dev/test.
  * Use **OPTIMIZE** + **Z-order** for large fact tables; record time spent, as it’s part of cost.
* **Snowflake**

  * Size warehouses for concurrency tests; leverage **auto-suspend** at 60–300 seconds realistically.
  * Consider **clustering keys** only for very large tables with known filter columns; track recluster cost.
* **Databricks**

  * Use **Photon** on SQL warehouses; set **channel** to current.
  * **Unity Catalog** for governance and consistent access; OPTIMIZE with **ZORDER** on join/filter columns.
* **Synapse**

  * For dedicated pools, choose distribution (HASH/ROUND_ROBIN); keep CTAS patterns for large load.
  * Update statistics after large loads; partition big tables by date.

### Appendix B: Data Correctness Validation

* Row counts per table
* Aggregation totals per business key
* Random sample checksums
* Deterministic business queries returning identical results

### Appendix C: Risk Register Template

| ID | Risk               | Likelihood | Impact | Mitigation                                            | Owner          |
| -: | ------------------ | ---------: | -----: | ----------------------------------------------------- | -------------- |
|  1 | Non-portable SQL   |        Med |   High | Maintain translation layer                            | Data Eng       |
|  2 | Cost spikes        |        Med |   High | Auto-suspend; schedule windows                        | FinOps         |
|  3 | Streaming backlogs |        Low |    Med | Backpressure tests                                    | Platform       |
|  4 | Biased caching     |        Med |    Med | Separate cold/warm; disable result cache where needed | Benchmark Lead |

---

**Final note**: This framework favors **clarity over hype**. It won’t crown a universal winner. It will show which platform wins **for your workloads, your scale, and your operating model**—which is the only benchmark that matters.
