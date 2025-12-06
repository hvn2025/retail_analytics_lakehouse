# retail_analytics_lakehouse
An end-to-end Databricks project that ingests retail sales, customer, and order data, transforms it through the Medallion architecture (Bronze, Silver, Gold), and delivers analytics-ready outputs to uncover trends, brand performance, and customer insights.

## 📂 Project Structure

* **`Data_Files/`**: Contains the raw CSV/JSON source data (Orders, Customers, Products, Regions).
* **`setup_catalog.ipynb`**: The initialization script to set up the Unity Catalog (or Hive Metastore) and Databases.
* **`medallion_procesing/`**: Notebooks responsible for the ETL pipeline (Bronze ingestion, Silver cleaning, Gold aggregation).

---

## 🚀 Execution Guide

Follow these steps to deploy and run the pipeline.

### Step 1: Environment Setup (Catalog & Database)
Before processing any data, we must define the governance structure for our Delta tables.

1.  Open the `setup_catalog.ipynb` notebook in your Databricks Workspace.
2.  **Run all cells**.
    * **Goal:** This script creates the **Catalog** (the highest level of organization) and the **Database (Schema)**. It ensures that the Delta Lake environment is ready to register tables.

### Step 2: Upload Data Files
We need to move the local data files into the cloud environment for Databricks to access.

1.  Locate the `Data_Files/` folder in this repository.
2.  Upload these files to **Databricks Volumes** or **DBFS (Databricks File System)**.
    * *Recommended Path:* `/Volumes/retail_catalog/retail_schema/raw_data/` (if using Unity Catalog) or `dbfs:/FileStore/tables/retail_project/`.
3.  **Note:** Ensure the file paths in the processing notebooks match your specific upload location.

### Step 3: Medallion Processing (Bronze & Silver)
This stage involves ingesting raw data and cleaning dimensions.

1.  Navigate to the `medallion_procesing/` folder.
2.  Run the **Bronze/Silver** notebooks.
    * **Bronze Layer:** Reads the raw CSVs and saves them as Delta tables (preserves raw history).
    * **Silver Layer (Dimension Processing):**
        * Cleans customer and product data (Handling NULLs, deduplication).
        * Enforces Schema validation.
        * **Fact Generation:** Parses the Sales data to generate additional analytic fields, including:
            * `Quarter` (derived from Order Date).
            * `Region_ID` association.
            * `Category_ID` association.

### Step 4: Gold Layer & Reporting Aggregations
In this step, we generate specific business aggregations to answer key questions regarding sales performance.

1.  Run the **Gold** processing logic.
2.  The pipeline generates specific "Fact" tables focusing on:
    * **Sales by Region:** Aggregated revenue grouped by geographical location.
    * **Quarterly Sales:** Time-series data tracking performance Q1-Q4.
    * **Category Sales:** Performance metrics per product category.

### Step 5: Final Denormalization (Dashboard Ready)
To ensure tools like PowerBI, Tableau, or Databricks SQL Dashboards perform efficiently, we flatten the data model.

1.  **The Denormalization Step:** The final notebook joins the Fact tables (Sales) with all Dimension tables (Customers, Products, Regions).
2.  **Outcome:** A single, wide **"One Big Table" (OBT)** in the Gold layer.
    * This removes the need for complex joins within the Dashboard tool.
    * Enables low-latency querying for end-users.

---

## 📊 Architecture Overview

This project utilizes the **Lakehouse** paradigm:

* **Bronze:** Raw copy of data from source.
* **Silver:** Filtered, cleaned, and augmented data (Transformation & Schema Enforcement).
* **Gold:** Business-level aggregates and denormalized tables ready for visualization.

## 🛠 Technologies Used
* **Databricks** (Compute & Orchestration)
* **Apache Spark** (PySpark & SQL)
* **Delta Lake** (Storage Layer)
* **Unity Catalog** (Data Governance)
