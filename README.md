Tokyo Olympic Data Analytics | End-to-End Azure Data Engineering Pipeline

Overview

This project delivers a complete, cloud-native data engineering pipeline designed to ingest, process, transform, and serve the 2021 Tokyo Olympic Games dataset. 

The primary business goal is to transform raw CSV datasets containing information on athletes, coaches, teams, entries by gender, and medal tallies into refined analytical datasets. Raw datasets are hosted directly in this GitHub repository and fetched dynamically using GitHub Raw URLs via Azure Data Factory. Using an automated cloud architecture, this engineering solution prepares high-performance Parquet datasets and queryable SQL views, serving them directly to the Data Analytics / BI team for downstream reporting and dashboard creation. 

Architecture Diagram

Below is the end-to-end data pipeline architecture spanning ingestion, storage, transformation, serving, and handoff layers:

![image alt](https://github.com/s-h-naveen/tokyo-olympic-azure-data-engineering/blob/04ee85e27480bd1e4baedd700a6e0e31fb996c06/architecture-diagram.jpg)

Tech Stack

Version Control & Source Data: GitHub (GitHub Raw Content Endpoints) 
Data Ingestion: Azure Data Factory (ADF) 
Data Storage: Azure Data Lake Storage Gen2 (ADLS Gen2) 
Data Transformation & Compute: Azure Databricks, Apache Spark (PySpark) 
Data Warehousing & Querying: Azure Synapse Analytics (Serverless SQL Pools) 

Step-by-Step Setup Guide

1. Provision Infrastructure & Authentication
1.	Create an Azure Resource Group named tokyo-olympic in region Southeast Asia. 
2.	Provision an ADLS Gen2 storage account named tokyoolympicdata with Hierarchical Namespace enabled. 
3.	Create a container named tokyo-olympic-data containing two subdirectories: raw-data and transformed-data. 
4.	Register an Azure Active Directory App Service Principal (app-tokyo-olympic), generate a Client Secret, and assign the Storage Blob Data Contributor role on the storage account. 

2. Configure Ingestion (Azure Data Factory)
1.	Provision Azure Data Factory workspace tokyo-olympic-df. 
2.	Set up an HTTP Linked Service pointing to your GitHub repository's raw URL base path:
Example: https://raw.githubusercontent.com/<YOUR_GITHUB_USERNAME>/<YOUR_REPO_NAME>/main/data/
3.	Build a pipeline named data-ingestion containing Copy Data activities that fetch each raw CSV file (Athletes.csv, Coaches.csv, EntriesGender.csv, Medals.csv, Teams.csv) from GitHub and store them in tokyo-olympic-data/raw-data. 

3. Transformation (Azure Databricks)
1.	Provision Azure Databricks workspace tokyo-olympic-db and start a single-node cluster. 
2.	Mount the ADLS Gen2 tokyo-olympic-data container using the Service Principal credentials. 
3.	Run the transformation notebook Tokyo Olympic Transformation.ipynb: 
•	Read raw CSVs from /mnt/tokyoolympic/raw-data/ into PySpark DataFrames. 
•	Enforce explicit schema definitions, clean numeric data types, and handle missing values. 
•	Export the processed data back to tokyo-olympic-data/transformed-data/ in optimized Snappy-compressed Parquet format. 

4. Data Serving & Analytics (Azure Synapse Analytics)
1.	Provision Azure Synapse Workspace tokyo-olympic-sa and connect to the Lake database TokyoOlympicDB. 
2.	Create external tables pointing to the Parquet files in the transformed-data folder. 
3.	Execute Serverless SQL analytical scripts to validate data schema and pre-aggregate core metrics. 

5. Analytics Team Handoff & Data Consumption
Provide the Data Analytics / BI team with read access to the transformed data layer via either of the following endpoints:
•	Serverless SQL Endpoint: Expose Azure Synapse views and external tables (TokyoOlympicDB) for direct SQL connectivity via Power BI, Tableau, or Looker Studio. 
- Server Endpoint: tokyo-olympic-sa-ondemand.sql.azuresynapse.net
- Database Name: TokyoOlympicDB
•	Direct Storage Endpoint: Grant read access to the underlying Parquet files located at abfss://tokyo-olympic-data@tokyoolympicdata.dfs.core.windows.net/transformed-data/ for custom analytics pipelines. 

Key Insights (Pre-aggregated SQL Validation)
Based on pre-aggregated SQL queries executed in Azure Synapse Analytics prior to BI handoff: 
•	Top Country Participation: The United States of America and Japan sent the largest athlete contingents across the highest number of overall disciplines. 
•	Medal Dominance: The medal tally was led by top-performing nations maintaining a strong correlation between total team size and cumulative gold medal counts. 
•	Gender Balance across Disciplines: Certain sports (such as Swimming and Athletics) achieved high parity in male and female entry counts, whereas specific niche disciplines displayed significant variance in total participant entries. 

<img width="451" height="659" alt="image" src="https://github.com/user-attachments/assets/bd3b3bc7-f09a-4ce6-a684-c98c058b1302" />
