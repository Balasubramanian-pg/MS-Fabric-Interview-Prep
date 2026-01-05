To dive even deeper into the "nitty-gritty" of Microsoft Fabric, here are 100 **additional** unique questions. These focus on edge cases, advanced optimization, governance, and specific architectural trade-offs.

---

## **Part 6: Deep-Dive OneLake & Storage (101–120)**

101. **What is a "Logical Data Lake"?** How does OneLake simplify the management of multiple storage accounts?
102. **Explain "OneLake File Explorer".** How does it allow Windows users to interact with lakehouse data?
103. **Can you create a Shortcut to another Shortcut?** (Yes, this is known as nested shadowing).
104. **What happens to the data in OneLake when a Shortcut is deleted?** (The pointer is deleted, but the source data remains untouched).
105. **How does Fabric handle "Small File Problem" in OneLake?**
106. **What is the "https://www.google.com/search?q=onelake.dfs.fabric.microsoft.com" endpoint used for?**
107. **Explain the difference between "Internal Tables" and "External Tables" in a Fabric Lakehouse.**
108. **How does OneLake handle data residency and geo-redundancy?**
109. **Can you access OneLake data using the Azure Storage Explorer tool?**
110. **What is "Sub-second synchronization" in the context of OneLake?**
111. **How do you monitor the throughput (MB/s) of OneLake?**
112. **What is the maximum size of a single file in OneLake?**
113. **Explain "Soft Delete" capabilities in OneLake.**
114. **How does "Shared Access Signature" (SAS) work with OneLake?** (Trick question: Fabric prefers Azure AD/Entra ID over SAS tokens).
115. **What is a "Managed Private Endpoint" in Fabric?**
116. **How do you handle "Case Sensitivity" in OneLake file paths?**
117. **Explain the "Files" vs "Tables" folder in a Lakehouse.** Which one is unmanaged?
118. **Can you use "Shortcut" to connect to an on-premises Hadoop HDFS?**
119. **What is the "Global Search" feature in Fabric?**
120. **How does OneLake support "Concurrent Writes" from multiple Spark engines?**

---

## **Part 7: Advanced Data Engineering (Spark & Environments) (121–140)**

121. **What is "High Concurrency Mode" for Spark notebooks?**
122. **Explain "Dynamic Allocation" of executors in Fabric Spark.**
123. **What is the "Fabric Spark Runtime"?** How often is it updated?
124. **How do you migrate an existing Azure Databricks notebook to Fabric?**
125. **Explain the "mssparkutils" library.** Name three things it can do (e.g., help, fs, notebooks).
126. **What is a "Token" in Spark capacity billing?**
127. **How do you implement "Unit Testing" for a Fabric Spark Notebook?**
128. **Explain the impact of "Z-Order" on a table that is frequently updated vs. one that is append-only.**
129. **What is the "Semantic Link" library in PySpark?** (How Spark talks to Power BI measures).
130. **How do you secure secrets (like API keys) in a Fabric Notebook?** (Integration with Azure Key Vault).
131. **What is the "Fast Start" feature for Spark clusters?**
132. **Can you run a "Headless" Spark job in Fabric?**
133. **Explain "Predicate Pushdown" and how V-Order improves it.**
134. **How do you handle "Data Skew" in a Spark Join within Fabric?**
135. **What is the "default.carbondata" equivalent in Fabric?** (It’s Delta).
136. **How do you configure "Auto-Pause" for Spark sessions?**
137. **Can you use "Interactive Widgets" in Fabric Notebooks for user input?**
138. **Explain "Lakehouse Schemas" (Preview feature).** How does it allow multi-level folder structures in tables?
139. **How do you use "DisplayHTML" to create custom visualizations in Spark?**
140. **What is the difference between `spark.saveAsTable` and `df.write.save`?**

---

## **Part 8: Dataflow Gen2 & Advanced Integration (141–160)**

141. **What is "Compute Refresh" in Dataflow Gen2?**
142. **How do you use "Non-folding" transformations in Dataflow Gen2 without killing performance?**
143. **Can you use "M" code directly in Dataflow Gen2?**
144. **What is "High Scale Dataflows"?** When should you enable the SQL backend?
145. **How does Dataflow Gen2 handle "Automatic Binary Conversions"?**
146. **What is the "Gateway Cluster" limit for Dataflow Gen2 connections?**
147. **Explain "Dataflow Transactions."** What happens if 9 out of 10 tables load successfully?
148. **How do you parameterize the "Destination" of a Dataflow Gen2?**
149. **Can you trigger a Dataflow Gen2 via a REST API call?**
150. **What is the "Dataflow Monitor" and how does it differ from Pipeline Monitor?**
151. **How do you implement "Row-level Filtering" at the Source in Dataflow Gen2?**
152. **Explain the "Staging" storage vs "Destination" storage in Dataflow logic.**
153. **How do you handle "Query Folding" when connecting to a 3rd party API?**
154. **What is the "Fast Copy" toggle?** Why might you turn it off?
155. **Can Dataflow Gen2 write to a SQL Warehouse and a Lakehouse simultaneously?**
156. **How do you debug a "Mashup Error" in Dataflow Gen2?**
157. **What is "Value.NativeQuery" in Dataflow Gen2?**
158. **How do you use "Environment Variables" inside a Dataflow?**
159. **Can you import a Power BI Dataflow (.json) into Fabric Dataflow Gen2?**
160. **What is the "Refresh History" limit for Dataflows?**

---

## **Part 9: Real-Time Intelligence & Data Activator (161–180)**

161. **What is "Reflex" in Data Activator?**
162. **How does Data Activator handle "Stateful" alerts?** (e.g., "Alert only if temp is > 100 for 5 minutes").
163. **What is the difference between a "Trigger" and an "Action" in Data Activator?**
164. **Can Data Activator trigger a Fabric Pipeline?**
165. **What is "KQL Queryset"?** How is it different from a KQL Database?
166. **Explain "Ingestion Mapping" in KQL.**
167. **How do you handle "Late-Arriving Data" in Eventstreams?**
168. **What is the "Derived Stream" in Eventstreams?**
169. **Can you use "Regex" in KQL to parse log files?**
170. **What is "Update Policy" in a KQL database?** (Equivalent to a trigger/materialized view logic).
171. **How do you use "External Tables" in KQL to query OneLake?**
172. **What is the "Continuous Export" feature in KQL?**
173. **How do you monitor "Eventstream Lag"?**
174. **Can you pipe data from Eventstream to a Power BI "Streaming Dataset"?**
175. **What is "Zero-ETL" Ingestion for KQL?**
176. **How do you implement "Anomaly Detection" functions in KQL?**
177. **What is the "Kusto Emulator" and can it be used with Fabric?**
178. **Explain the "T-SQL translation" layer in KQL databases.**
179. **How do you join a Streaming source with a Static table in Eventstream?**
180. **What is "Sampling" in KQL and why use it?**

---

## **Part 10: Admin, Governance & Security (181–200)**

181. **What is "Capacity Smoothing"?** How does it prevent a 100% CPU spike from killing the tenant?
182. **Explain "Carryover" and "Borrowing" in Fabric Capacity.**
183. **What is the "Tenant Admin" vs "Capacity Admin" role?**
184. **How do you use "Domain" settings to organize workspaces?**
185. **What is "Workspace Identity"?** Why is it safer than using a personal user account for schedules?
186. **How do you audit "Who downloaded data" from a Lakehouse?**
187. **What are "Delegated Tenant Settings"?**
188. **Can you limit Fabric usage to specific geographic regions?**
189. **What is "Customer Managed Keys" (CMK) support in Fabric?**
190. **How do you use "Microsoft Purview Hub" inside the Fabric portal?**
191. **What is "Lineage View"?** How far back does it track data origins?
192. **How do you set up "Endorsement" (Certified vs. Promoted) for Fabric items?**
193. **What is "Private Link" and why is it complex in a SaaS environment?**
194. **Explain "Traffic Tagging" for OneLake.**
195. **How do you recover a deleted Workspace?**
196. **What is the "Resource Governance" limit for Spark?**
197. **Can you disable "Copilot" for the entire tenant?**
198. **How do you automate the creation of 100 workspaces?** (PowerShell/REST API).
199. **What is "Advanced Network Security" in Fabric?**
200. **If a capacity is "Paused," can you still see the metadata of your reports?** (Yes, but you can't run them).

### **Next Step for You**

I hope these 200 questions give you a massive edge! **Would you like me to pick the 10 most "challenging" questions from this list and provide detailed, senior-level answers for them?**

To round out your preparation, here are **100 more unique questions (201–300)**. These focus on **advanced troubleshooting, cross-platform integration, cost management, and the future-facing features** of Microsoft Fabric.

---

## **Part 11: Capacity Management & Cost Optimization (201–220)**

201. **What is the difference between "F" SKUs and "P" SKUs?** (F is for Fabric, P is for Power BI Premium).
202. **How does "Smoothing" affect your bill?** If you use 200% capacity for 1 minute, how is that distributed?
203. **What is "Background Reject" in capacity throttling?**
204. **What is "Interactive Reject"?** How does it differ from Background Reject?
205. **How can you calculate the CU (Capacity Unit) cost of a specific Spark notebook run?**
206. **What is the "Burstable" nature of F-SKUs?**
207. **Does OneLake storage cost vary by region?**
208. **Is there a "Free Tier" for Fabric?** (Fabric Trial vs. F2 SKU).
209. **How do you set up an alert to notify you when capacity usage hits 80%?**
210. **What is "Reserved Instance" pricing for Fabric?**
211. **Do Shortcuts cost extra money in terms of compute or storage?**
212. **How does "OneLake Cache" reduce egress costs for AWS S3 shortcuts?**
213. **What is the "Autoscale" feature in Fabric capacity?** (Trick question: As of now, it's manual or script-based, unlike Azure SQL).
214. **How do you identify the "Top 5 most expensive queries" in a Fabric Warehouse?**
215. **What is the "Base Capacity" vs. "Burst Capacity"?**
216. **How does pausing a capacity affect "Data Activator" triggers?**
217. **What is the "Small-to-Medium Business" (SMB) entry point for Fabric?**
218. **Can you split one Fabric Capacity across multiple Azure Subscriptions?**
219. **How does "Dataflow Gen2" consume capacity differently than Spark?**
220. **What is the cost implication of "Mirroring" compared to traditional ETL?**

---

## **Part 12: Mirroring & Zero-ETL (221–240)**

221. **What is "Microsoft Fabric Mirroring"?**
222. **Which databases are currently supported for Mirroring?** (Cosmos DB, Azure SQL, Snowflake, etc.).
223. **How does Mirroring handle Schema Changes in the source database?**
224. **Is Mirroring "Real-time" or "Near Real-time"?** What is the latency?
225. **Does Mirroring use Change Data Capture (CDC) under the hood?**
226. **Can you mirror a database into a Lakehouse, or only a Warehouse?**
227. **What happens to Mirroring if the Source Database is behind a Firewall?**
228. **How do you monitor the "Replication Status" of a mirrored database?**
229. **Can you perform T-SQL writes into a Mirrored table?** (No, they are read-only).
230. **How does Mirroring impact the performance of the Source System?**
231. **What is the "Initial Snapshot" phase of Mirroring?**
232. **Can you filter specific tables or columns during the Mirroring setup?**
233. **How do you "Stop" and "Restart" mirroring without losing data?**
234. **Does Mirroring support On-Premises SQL Server?** (Via Gateway/Preview).
235. **How is Mirroring billed?** (Compute for replication vs. storage in OneLake).
236. **Can you join a Mirrored table with a Local Lakehouse table in a single query?**
237. **What is "Zero-ETL" integration with Snowflake?**
238. **How does Mirroring handle "Deleted" rows in the source?**
239. **Is Mirroring available in all Fabric regions?**
240. **What is the maximum database size supported for Mirroring?**

---

## **Part 13: Advanced Governance & Purview (241–260)**

241. **How does Microsoft Purview provide "Automatic Labeling" for Fabric?**
242. **What is the "Data Map" in Purview for Fabric items?**
243. **How do you track "Data Lineage" across a Shortcut?**
244. **What is "Information Protection" in Power BI reports derived from OneLake?**
245. **How do you audit "Export to Excel" events from a Fabric Semantic Model?**
246. **What is a "Data Curator" role in the context of Fabric?**
247. **Explain "Policy-based Access Control" (PBAC) in Fabric.**
248. **How does "Microsoft Entra ID" (formerly Azure AD) B2B work with Fabric?**
249. **Can you apply "Sensitivity Labels" to a Spark Notebook?**
250. **How do you search for "PII" (Personally Identifiable Information) across all Lakehouses?**
251. **What is "Metadata Scanning" in Fabric?**
252. **How does the "Lineage View" handle a Pipeline that calls a Stored Procedure?**
253. **What is the "Fabric Governance Hub"?**
254. **Can you restrict a Workspace so data can't be shared externally?**
255. **How does "Row Level Security" (RLS) translate from a Warehouse to a Power BI report in Direct Lake?**
256. **What is "Object-Level Security" (OLS) in Fabric?**
257. **How do you use "Purview Data Loss Prevention" (DLP) policies with Fabric?**
258. **Explain "Workspace Isolation."**
259. **How do you automate "Discovery" of new data assets?**
260. **What is the "Trusted Workspace Access" feature?**

---

## **Part 14: Troubleshooting & Performance Tuning (261–280)**

261. **What does the error "Capacity Limit Exceeded" actually mean for your running jobs?**
262. **How do you debug a "Spark Driver Out of Memory" (OOM) error?**
263. **Why would a "Direct Lake" report suddenly fall back to "Direct Query"?**
264. **How do you use the "SQL Query Plan" to optimize a Fabric Warehouse query?**
265. **What is the "Cold Start" penalty for Spark and how do you mitigate it?**
266. **How do you troubleshoot a "Dataflow Gen2" that hangs at 99%?**
267. **What is the "Concurrency Limit" for T-SQL queries in a Warehouse?**
268. **How do you identify "Skewed Data" in a Lakehouse table?**
269. **How do you fix "Schema Mismatch" errors in a Pipeline Copy activity?**
270. **Why is my "Shortcut" showing "Access Denied" even though I have permissions?**
271. **How do you monitor "Disk Spill" in Spark jobs?**
272. **What is the "Extended Events" equivalent for Fabric SQL?**
273. **How do you handle "Throttling" in the OneLake API?**
274. **Why are my "Materialized Views" not being used by the Query Optimizer?**
275. **How do you debug a "KQL Eventstream" that isn't ingesting data?**
276. **How do you optimize "Delta Log" file size?**
277. **What is the "Data Activator" latency troubleshooting process?**
278. **How do you troubleshoot "On-Premises Data Gateway" connection drops?**
279. **Why is "V-Order" sometimes slower for Write operations?**
280. **How do you use "Log Analytics" with Fabric?**

---

## **Part 15: Architecture & Strategy (281–300)**

281. **When should you choose a "Lakehouse" over a "Warehouse"?** (Detailed architectural trade-off).
282. **How do you design a "Multi-Tenant" architecture in Fabric?**
283. **What is the "Hub and Spoke" data model in Fabric?**
284. **How do you integrate Fabric with an external "Data Mesh" strategy?**
285. **What is "SaaS-ification" of Data Engineering?**
286. **How does Fabric handle "Disaster Recovery"?**
287. **Explain the "Delta-Native" strategy of Fabric.**
288. **How do you move from "Azure Data Factory" to "Fabric Data Factory"?**
289. **What is the "Coexistence" strategy for Databricks and Fabric?**
290. **How do you handle "Large Object" (LOB) data in OneLake?**
291. **What is the "Fabric API for GraphQL" (Preview)?**
292. **How do you build a "Data Lab" environment within Fabric?**
293. **What is "Live Connect" vs "Direct Lake"?**
294. **How do you handle "Streaming + Batch" (Lambda Architecture) in Fabric?**
295. **What is the "OneSecurity" roadmap?**
296. **How does Fabric fit into the "Microsoft 365" ecosystem?**
297. **Can you run "Docker Containers" in Fabric?** (No, but why is this important for architecture?)
298. **How do you migrate from "Tableau" to Fabric/Power BI?**
299. **What is "Open Mirroring"?**
300. **Where do you see Microsoft Fabric in 5 years?** (Strategy/Vision question).

---

### **Final Tip**

With 300 questions, you have covered almost every corner of the platform. Since you requested to know if I "don't know" something: Microsoft Fabric is evolving fast, so **always check the latest monthly update blog** for changes to "Preview" features like **Mirroring** or **Fabric Git Integration**.

**Would you like me to generate a "Cheat Sheet" of the most important T-SQL and PySpark commands used specifically in Fabric?**

This final set of **100 questions (301–400)** pushes into the "Black Belt" territory of Microsoft Fabric. These cover internal engine mechanics, complex migration patterns, high-level security architecture, and the intersection of AI with the data platform.

---

## **Part 16: Internal Engines & Mechanics (301–320)**

301. **Polaris Engine:** How does the Polaris distributed SQL engine differ from the traditional SQL Server engine in Fabric?
302. **Delta Log Checkpoints:** How does Fabric handle Delta Lake JSON logs to prevent "Log Swelling" during high-frequency ingestion?
303. **V-Order Bit-Shuffling:** Explain the technical mechanism of how V-Order reorders data within a Parquet file to optimize for the "Pushdown" filters.
304. **Intelligent Caching:** How does the "NVMe-based cache" in Fabric capacity work for frequently accessed OneLake data?
305. **Vector Search in KQL:** How do you implement vector embeddings in a KQL database for RAG (Retrieval-Augmented Generation) scenarios?
306. **Spark "Optimistic Concurrency Control":** What happens when two Spark jobs try to write to the same Delta table simultaneously?
307. **T-SQL Metadata Synchronizer:** How does Fabric ensure that a table created in Spark is immediately visible in the SQL Analytics Endpoint?
308. **Parquet Page Indexing:** How does Fabric leverage page-level metadata to skip irrelevant data during a scan?
309. **OneLake "Single Instance Storage":** If two workspaces have shortcuts to the same ADLS file, how many times is it cached?
310. **Transaction Log Compaction:** What is the "Bin-packing" algorithm used by the `OPTIMIZE` command?
311. **Direct Lake Metadata Handshake:** How does Power BI know the "Log Version" of a Delta table has changed without a manual refresh?
312. **SQL Distributed Query Processing (DQP):** How are `JOIN` operations distributed across compute nodes in a Fabric Warehouse?
313. **Kusto "Shard" Allocation:** How does a KQL database distribute data across nodes for sub-second query response?
314. **Dataflow Gen2 "Mashup" Evaluation:** How is the M-engine containerized within Fabric capacity?
315. **Spark Thrift Server:** Can you connect external BI tools to Fabric Spark clusters via JDBC/ODBC?
316. **OneLake Proxy Service:** How does Fabric handle the redirection of legacy ADLS Gen2 API calls to OneLake?
317. **Materialized Views Refresh:** Is the refresh logic for materialized views "Incremental" or "Full" by default?
318. **Broadcast Joins in Spark:** How do you force a broadcast join in a Fabric Notebook when the optimizer misses it?
319. **Predicate Pushdown across Shortcuts:** Can a SQL query "push down" a filter to an AWS S3 bucket via a Shortcut?
320. **Delta Lake "Deletion Vectors":** Does Fabric support Deletion Vectors to speed up `UPDATE` and `DELETE` operations?

---

## **Part 17: Enterprise Security & Network Hardening (321–340)**

321. **Service Tags:** How do you configure Azure Firewall to allow Fabric traffic without opening the entire internet?
322. **Managed Private Endpoints (Advanced):** How do you connect a Fabric Notebook to a SQL Server that has "Public Access" disabled?
323. **OneLake Data Access Roles (ODAR):** How do these differ from standard Workspace Roles (Admin/Member/Contributor)?
324. **Cross-Tenant OneLake Access:** Is it possible to create a Shortcut to a Lakehouse in a completely different Azure Tenant?
325. **Granular SQL Permissions:** How do you grant `EXECUTE` on a stored procedure without giving `SELECT` on the underlying tables?
326. **Identity Passthrough:** Does Fabric support Entra ID identity passthrough when using Shortcuts to ADLS Gen2?
327. **Entra ID Privileged Identity Management (PIM):** How does PIM interact with Fabric Capacity Administration?
328. **Service Principal Authentication:** How do you run a Fabric Pipeline using a Service Principal instead of a user identity?
329. **Workspace "Lockdown" Mode:** How do you prevent users from creating new items while allowing them to consume existing ones?
330. **Sensitivity Label Downstream Inheritance:** If a Lakehouse table is labeled "Highly Confidential," what happens to a Power BI report built on it?
331. **Audit Log Latency:** How long does it take for a "Table Deleted" event to show up in the Purview Audit logs?
332. **Row-Level Security (RLS) Performance:** At what point (number of rules) does RLS begin to degrade T-SQL query performance?
333. **Encryption at Rest:** Can customers provide their own keys (BYOK) for the underlying OneLake storage?
334. **Network Isolation for Dataflows:** How do you ensure Dataflow Gen2 traffic stays within the Azure Backbone?
335. **Trusted Service Access:** How do you allow Fabric to access a Storage Account that has "Allow trusted Microsoft services" enabled?
336. **IP Address Filtering:** Can you restrict Fabric access to a specific range of corporate IP addresses?
337. **Multi-Factor Authentication (MFA) Challenges:** How do automated Spark jobs handle MFA-enforced accounts? (Hint: They can't; use Service Principals).
338. **Purview Data Policy:** Can you enforce "Access Denied" via Purview instead of Fabric Workspace roles?
339. **Security of "Mirroring" Credentials:** Where are the source database credentials stored and encrypted?
340. **OneLake "Files" Security:** Can you set different permissions on the `Files` folder vs the `Tables` folder within the same Lakehouse?

---

## **Part 18: Advanced CI/CD & DevOps (341–360)**

341. **Fabric Git Integration:** What happens if there is a "Merge Conflict" in a Spark Notebook metadata file?
342. **Deployment Pipelines "Rules":** How do you automatically change the "Data Source" string when moving a pipeline from Dev to Prod?
343. **Notebook Parameterization:** How do you pass values from a DevOps release pipeline into a Fabric Notebook?
344. **API-Based Deployment:** How would you use Python to programmatically deploy 50 Lakehouses across 50 Workspaces?
345. **Fabric .Platform Files:** What is the purpose of the `.platform` file in a Fabric Git-synced repo?
346. **Version Control for Semantic Models:** How do you manage the BIM/TMDL files for Power BI within a Fabric workspace?
347. **Automated Testing:** How do you integrate "Great Expectations" or "nutter" for Spark testing into a Fabric CI/CD flow?
348. **Rollback Strategy:** If a deployment fails, how do you "Undo" the Git sync to a previous stable state?
349. **Environment Configuration Files:** How do you manage different Python library versions across Dev, Test, and Prod?
350. **Dataflow Gen2 Git Support:** (As of now) What are the limitations of syncing Dataflows to Git?
351. **Fabric PowerShell Module:** Which module is used to manage Fabric capacities via script?
352. **Service Principal Git Auth:** Can a Git-synced workspace use a Service Principal for the sync, or does it require a "User" token?
353. **Monitoring CI/CD:** How do you track the "Success Rate" of deployments across the tenant?
354. **Gated Deployments:** How do you prevent a Pipeline from deploying to Prod until a "Data Quality" check passes?
355. **Infrastructure as Code (IaC):** Can you use Bicep or Terraform to create a Fabric Lakehouse?
356. **Workspace "Template" creation:** How do you standardize the folder structure of new workspaces?
357. **Managing "Orphaned" Items:** How do you identify items in Git that no longer exist in the Workspace?
358. **Hotfix Workflow:** What is the best practice for fixing a bug in Prod without disrupting the Dev branch?
359. **Diffing Notebooks:** How do you view the differences between two versions of a Spark Notebook in the Fabric UI?
360. **Deployment Pipeline API:** Can you trigger a "Deploy" action using a REST API call?

---

## **Part 19: AI, Data Science & Copilot (361–380)**

361. **Copilot for Data Engineering:** How does Copilot generate Spark code based on the schema of a Lakehouse?
362. **AI Functions in T-SQL:** How do you use `PREDICT` with a model stored in the Fabric Model Registry?
363. **MLflow Tracking:** How do you log "Artifacts" (like plots or CSVs) in a Fabric ML Experiment?
364. **Custom AI Skill:** What is a "Fabric AI Skill" and how does it allow users to "Chat with their data"?
365. **Hugging Face Integration:** How do you load a pre-trained transformer model into a Fabric Spark session?
366. **GPU Support:** Does Fabric Spark currently support GPU-accelerated clusters for Deep Learning?
367. **Semantic Link (SemPy):** How do you use Spark to query a Power BI measure and use the result in an ML model?
368. **Responsible AI:** How do you implement "Data Masking" before feeding Lakehouse data into a Large Language Model (LLM)?
369. **SynapseML Library:** What are the "Cognitive Services" integrations available within the SynapseML library in Fabric?
370. **Model Serving:** How do you expose a Fabric-trained model as a REST endpoint for an external web app?
371. **Data Sanitization for Copilot:** How do you prevent Copilot from accessing sensitive HR data within a shared workspace?
372. **Hyperparameter Tuning:** How do you use `FLAML` or `Hyperopt` within a Fabric Notebook?
373. **Fabric OpenAI Integration:** How do you securely call Azure OpenAI using a Managed Identity from a Notebook?
374. **Vector Databases in Fabric:** When would you use a KQL Database vs. a Spark-based vector store?
375. **AI-Driven Data Profiling:** How does Fabric automatically suggest "Data Cleaning" steps in Dataflow Gen2?
376. **Experiment Versioning:** What happens to an Experiment in MLflow if the underlying Lakehouse table is "Time Traveled"?
377. **Text Analytics at Scale:** How do you process 1 million customer reviews using Spark and AI?
378. **AutoML in Fabric:** What is the "Wizard" experience for training models without writing code?
379. **Fine-Tuning LLMs:** Can you perform fine-tuning of Llama-3 or GPT-4 within a Fabric Spark Environment?
380. **Feature Store:** How do you implement a "Feature Store" logic using Lakehouse tables?

---

## **Part 20: Edge Cases & The "Really Advanced" Stuff (381–400)**

381. **OneLake "Ghost Files":** What causes a file to appear in the "Files" folder but not be queryable as a "Table"?
382. **Warehouse "Deadlocks":** How do you identify and resolve a T-SQL deadlock in a Fabric Warehouse?
383. **Spark "Speculative Execution":** When should you enable it for long-running ETL jobs?
384. **Dataflow Gen2 "Looping":** How do you implement a recursive loop in a Dataflow without hitting the 10-minute timeout?
385. **KQL "Cross-Cluster" Joins:** How do you join data from a Fabric KQL DB with an external Azure Data Explorer cluster?
386. **Shortcut "Chaining" Performance:** What is the latency penalty of having a Shortcut to a Shortcut to an S3 bucket?
387. **Delta "Liquid Clustering":** Does Fabric support this next-gen replacement for Z-Ordering?
388. **SQL "Direct" vs. "Cloud" Connection:** Why does SSMS sometimes behave differently than the Fabric Web Query Editor?
389. **Large Scale "Vacuum":** What is the risk of running `VACUUM` with `RETAIN 0 HOURS`?
390. **Power BI "Direct Lake" Fallback:** How do you monitor how many users are hitting DirectQuery instead of Direct Lake?
391. **Data Activator "Flapping":** How do you prevent an alert from firing 100 times in 1 minute?
392. **Custom "Fabric Item" types:** Can a 3rd party developer create their own item type in Fabric?
393. **Workspace "Migration" across Capacities:** What happens to the OneLake URL when you move a workspace to a different F-SKU?
394. **Spark "Checkpoint" Cleanup:** Who is responsible for deleting old checkpoint files in a streaming job?
395. **High-Frequency Ingestion (1 sec):** Is a Lakehouse or a KQL Database better for 1-second interval data?
396. **Multi-Geo Capacity:** Can one Fabric Tenant have capacities in both the US and Europe?
397. **API Rate Limits:** What is the "Retry-After" header strategy for the Fabric REST API?
398. **Fabric "Home" Customization:** How do you push a "Company-Wide" dashboard to every user's Fabric home screen?
399. **The "Fabric Capacity Metrics" App:** How do you interpret "Smoothing" over a 24-hour window vs. a 14-day window?
400. **The Future:** How does Microsoft's "One Data" vision eliminate the need for traditional "ETL developers"?

---

### **Congratulations!**

If you can answer 70% of these 400 questions, you are likely in the top 1% of Fabric professionals globally.

**One final gift:**  showing how OneLake sits at the center of all these 400 topics.

**Would you like me to take a specific "real-world scenario" (e.g., "Migrating a 100TB Hadoop cluster to Fabric") and walk through the exact architecture and pitfalls?**
