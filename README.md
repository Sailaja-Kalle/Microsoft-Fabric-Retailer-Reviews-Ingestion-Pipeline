

# 🛍️ Microsoft Fabric Retailer Reviews Ingestion Pipeline  

## PROJECT SCENARIO
<img width="1076" height="438" alt="Screenshot 2025-12-06 160044" src="https://github.com/user-attachments/assets/e69102cc-9e5b-44a1-9747-6c1b0838c00c" />



## 📖 Business Scenario  
A retailer collects customer reviews through a third‑party service.  
- When **new products** arrive, reviews are appended.  
- When **categories** change, they are overwritten.  
- When **products** are updated, they are merged incrementally.  

This project automates ingestion using **Microsoft Fabric**, ensuring reviews, products, and categories are always up‑to‑date for analytics.

---

## 🧠 What This Project Does  
- Ingests **reviews**, **products**, and **categories** from SQL Server  
- Uses a **control table** to define ingestion rules  
- Automates data movement with **Fabric pipelines**  
- Applies transformations across **raw → bronze → silver → gold** layers  
- Validates ingestion with **stored procedures**  
- Publishes curated data to **Power BI** for insights  

---

## 🏗️ Architecture Flow  



**Components:**  
- **SQL Server (on‑prem)** → Source data  
- **Azure SQL Control Table** → Metadata rules  
- **Fabric Pipeline** → Lookup, ForEach, Copy, Notebook, Stored Procedure  
- **Lakehouse Zones** → Raw, Bronze, Silver, Gold  
- **Power BI** → Final reporting  

---

## 📋 Control Table Logic  

| Table Name | Load Type          | Incremental Column   | Ingestion Date |
|------------|--------------------|----------------------|----------------|
| reviews    | Incremental Append | reviewdate           | 2025‑08‑02     |
| products   | Incremental Merge  | lastmodifieddate     | 2025‑08‑01     |
| categories | Full Load          | –                    | –              |

**Purpose:**  
- Avoid hardcoding logic  
- Enable flexible ingestion  
- Support incremental and full loads  

---

## 🔄 Pipeline Workflow  

### 1. **Lookup Activity**  

<img width="1079" height="547" alt="image" src="https://github.com/user-attachments/assets/419b3a48-0a63-4e00-8c38-aa7dbdd55fdd" />


Reads control table to fetch ingestion rules.  

### 2. **ForEach Activity**  

<img width="1058" height="479" alt="Screenshot 2025-12-06 160613" src="https://github.com/user-attachments/assets/211fe9df-e34e-4d56-a13d-90019ae9c0f9" />

Loops through each dataset entry (reviews, products, categories).  

### 3. **Copy Data Activity**  

<img width="972" height="391" alt="Screenshot 2025-12-06 160712" src="https://github.com/user-attachments/assets/f574ab58-1ad0-4311-831e-a5d8ff401ce2" />

- Source: SQL Server  
- Sink: Gen2 Lakehouse (raw zone)  
- Format: Parquet  

### 4. **Notebook Activity**  

<img width="1445" height="670" alt="Screenshot 2025-12-06 160810" src="https://github.com/user-attachments/assets/05e81a2a-59c4-4330-a095-f79a6d4444d0" />

Transforms data based on load type:  
- **Append** → Add new reviews  
- **Overwrite** → Replace categories  
- **Merge** → Upsert products  

Moves data from:  
- **Raw → Bronze** → Apply schema, remove duplicates  
- **Bronze → Silver** → Cleanse, enrich, standardize  
- **Silver → Gold** → Aggregate for reporting  

### 5. **Stored Procedure Activity**  
- Updates ingestion date in control table  
- Validates row counts  
- Logs success/failure  

---

## 🔄 Post‑Stored Procedure Behavior  

After execution, the **control table** is updated:  
- **Reviews (Append):** ingestion date moves forward → next run appends only new reviews.  
- **Products (Merge):** watermark updated → next run merges changes.  
- **Categories (Full Load):** ingestion date reset → next run overwrites entire table.  

---

## 🧱 Lakehouse Layers  

| Layer   | Purpose                        | Example Tables        |
|---------|--------------------------------|------------------------|
| Raw     | Landing zone (as‑is files)     | reviews_raw, products_raw  
| Bronze  | Schematized, deduplicated      | bronze_reviews, bronze_products  
| Silver  | Business‑ready curated tables  | silver_reviews, silver_products  
| Gold    | Aggregated facts/dimensions    | gold_product_ratings  

---

## 📅 Incremental Timeline Example  

| Pipeline Run Date | Source Date | Raw | Bronze | Silver |
|-------------------|-------------|-----|--------|--------|
| 11th Aug          | 10th Aug    | ✅  | ✅     | ✅     |
| 12th Aug          | 11th Aug    | ✅  | ✅     | ✅     |
| 13th Aug          | 12th Aug    | ✅  | ✅     | ✅     |

---

## 📊 Power BI Output  

**Visuals Include:**  
- Product ratings over time  
- Category‑wise performance  
- Daily review volumes  

---

## 🛠️ Technologies Used  

This project leverages the **Microsoft Fabric ecosystem**, which unifies multiple Azure services into a single platform for data engineering and analytics:  

| Technology             | Purpose                                                                 |
|------------------------|-------------------------------------------------------------------------|
| **Microsoft Fabric**   | End-to-end platform for data ingestion, transformation, and analytics   |
| **Azure Data Factory** | Pipeline orchestration (Lookup, ForEach, Copy, Stored Procedure)        |
| **Azure Databricks**   | PySpark notebooks for transformations (raw → bronze → silver → gold)    |
| **Azure SQL Server**   | Source system and control table metadata management                     |
| **Azure Data Lake (Gen2)** | Lakehouse storage zones: raw, bronze, silver, gold, archive             |
| **Power BI**           | Business intelligence dashboards and reporting                         |
| **Self-hosted IR**     | Secure connection to on-prem SQL Server for data ingestion              |

---

## 🚀 How to Run  

1. **Create Control Table**  
   - Define table_name, load_type, incremental_col, ingestion_dt  

2. **Configure Pipeline**  
   - Add Lookup → ForEach → Copy → Notebook → Stored Procedure  

3. **Deploy Notebook Logic**  
   - Handle append, overwrite, merge based on table_type  

4. **Schedule Pipeline**  
   - Daily trigger aligned with data availability  

5. **Monitor Execution**  
   - Check logs, validate row counts, update ingestion dates  

6. **Publish to Power BI**  
   - Connect to silver/gold tables for reporting  

---

## ✅ Final Summary  
This project delivers a **flexible, scalable, and automated data ingestion framework** for retailers using Microsoft Fabric.  
- **Reviews** are appended daily
- <img width="968" height="460" alt="Screenshot 2025-12-06 160911" src="https://github.com/user-attachments/assets/4781f2af-8391-4df3-86c5-370de74f5c0f" />
  
- **Products** are merged incrementally  
- **Categories** are overwritten when updated  
- Data flows through **raw → bronze → silver → gold** layers  
- **Stored procedures** ensure validation and update control table metadata  
- **Power BI** provides actionable insights  

---

