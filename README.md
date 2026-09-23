# Tokyo Olympic Data Analytics: End-to-End Azure Data Engineering Pipeline

## Overview
This project delivers a complete, cloud-native data engineering pipeline designed to ingest, process, transform, and serve the 2021 Tokyo Olympic Games dataset.

The primary business goal is to transform raw CSV datasets containing information on athletes, coaches, teams, entries by gender, and medal tallies into refined analytical datasets. Raw datasets are hosted directly in this GitHub repository and fetched dynamically using GitHub Raw URLs via Azure Data Factory. Using an automated cloud architecture, this engineering solution prepares high-performance Parquet datasets and queryable SQL views, serving them directly to the Data Analytics / BI team for downstream reporting and dashboard creation.

---

## Architecture Diagram
Below is the end-to-end data pipeline architecture spanning ingestion, storage, transformation, serving, and handoff layers:

<p align="center">
  <img src="./architecture-diagram.jpg" alt="Tokyo Olympic Data Pipeline Architecture" width="100%">
</p>

---

## Tech Stack
* **Version Control & Source Data:** GitHub (GitHub Raw Content Endpoints)
* **Data Ingestion:** Azure Data Factory (ADF)
* **Data Storage:** Azure Data Lake Storage Gen2 (ADLS Gen2)
* **Data Transformation & Compute:** Azure Databricks, Apache Spark (PySpark)
* **Data Warehousing & Querying:** Azure Synapse Analytics (Serverless SQL Pools)

---

## Step-by-Step Setup Guide

### 1. Provision Infrastructure & Authentication
1. Create an Azure Resource Group named `tokyo-olympic` in region `Southeast Asia`.
2. Provision an ADLS Gen2 storage account named `tokyoolympicdata` with **Hierarchical Namespace** enabled.
3. Create a container named `tokyo-olympic-data` containing two subdirectories: `raw-data` and `transformed-data`.
4. Register an Azure Active Directory App Service Principal (`app-tokyo-olympic`), generate a Client Secret, and assign the **Storage Blob Data Contributor** role on the storage account.

---

### 2. Configure Ingestion (Azure Data Factory)
1. Provision Azure Data Factory workspace `tokyo-olympic-df`.
2. Set up an HTTP Linked Service pointing to your GitHub repository's raw URL base path:
   ```text
   [https://raw.githubusercontent.com/](https://raw.githubusercontent.com/)<YOUR_GITHUB_USERNAME>/<YOUR_REPO_NAME>/main/data/
