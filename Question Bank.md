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
