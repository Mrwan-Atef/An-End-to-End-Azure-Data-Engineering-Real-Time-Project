# End-to-End Azure Data Engineering Real-Time Project

## Overview
This project demonstrates a complete end-to-end data pipeline implementation using Azure resources for real-time data ingestion, transformation, analysis, loading, and reporting. The project integrates data from an on-premise SQL server into Azure, performs transformations, and creates interactive reports.

## Prerequisites
Ensure you have the following Azure services and tools installed:
- Azure Data Factory (ADF)
- Azure Databricks (ADB)
- Azure Synapse Analytics
- Azure Key Vault
- Azure Data Lake Storage (ADLS Gen 2)
- On-premise SQL Server
- Power BI Desktop

---

## Step 1: Create Resource Groups
1. Create the following resource groups:
   - **ADF (Azure Data Factory)**
   - **ADB (Azure Databricks)**
   - **Azure Synapse**
   - **Azure Key Vault**
   - **Azure Data Lake Storage (ADLS Gen2)**

---

## Step 2: Connect On-Premise SQL Server to Azure
### 2.1 Create SQL Authentication
- Create a **login** for the SQL instance using SQL authentication.
- Create a **user** for the database.

### 2.2 Configure Login & User
- Use the script `createlogin.sql` to create the login and user.
- Map the login to the user at the database level and assign read permissions.

### 2.3 Encrypt Secrets in Azure Key Vault
- Encrypt the username and password in **Azure Key Vault**.
- **Note**: If using RBAC (Role-Based Access Control), assign an admin role to the user to manage secrets.
- **Error**: If any resource wants to access the secret, it must be assigned a role. 
  - Solution: Assign a role to the resource (e.g., ADF) in **Azure Key Vault** to allow secret access.

![RBAC Configuration](/results of each step/1.png)

### Common Errors:
- **Error**: When encrypting secrets using RBAC, ensure that the admin role is properly assigned to create secrets.
- **Error**: When a resource (like ADF) wants to access the secret, ensure that a role is assigned to this resource in **Key Vault**.
- **Solution**: Assign necessary roles using **Role-Based Access Control (RBAC)**.

---

## Step 3: Data Ingestion using Azure Data Factory (ADF)
### 3.1 Establish SQL Server Connection
- Install the **Self-Hosted Integration Runtime** on a local or virtual machine.
- Configure the integration runtime in ADF.

### 3.2 Create Data Copy Pipeline
- Create a pipeline to copy one table (e.g., **address** table) from SQL Server to Azure.
- Use **Azure Key Vault secrets** for the database connection credentials.
- **Error**: You may face an issue where ADF cannot trust the server certificate chain.
  - Solution: Ensure the server certificate chain is trusted by the client.

### 3.3 Create Sink in ADLS Gen2
- Create the **sink** in **Azure Data Lake Storage Gen2** with **Parquet** format.
- Use the **bronze container** to store the raw data.

### 3.4 Run the Pipeline
- **Error**: Running the pipeline may throw an error if **Java Runtime Environment (JRE)** is not installed.
  - Solution: Install the necessary **JRE**.

![Pipeline Setup](https://drive.google.com/file/d/1pZDxPpjwByIL62dk72IK9IvsVZzBoc9c/view?usp=drive_link)

### 3.5 Create Dynamic Pipeline
- Create a pipeline that copies all tables using **Lookup** and **ForEach** activities.
- Use the `getschema.sql` script to get the schema name (e.g., **SalesLT**).
- Use dynamic content to loop over tables and copy them to the sink.
  
![Dynamic Pipeline](https://drive.google.com/file/d/16Idm7I0bAGiEqz_ROk2hqKklgWCZlfhw/view?usp=drive_link)

---

## Step 4: Data Transformation using Azure Databricks (ADB)
### 4.1 Create Databricks Compute Resource
- Create a **Databricks** compute cluster with a single node.
- Ensure Databricks has access to **Data Lake** (same admin role).

### 4.2 Create Notebooks for Transformation
- **Notebook 1**: Mount Azure Data Lake Storage for each container using `storgemount.ipynb`.
- **Notebook 2**: First transformation (Bronze to Silver) using `bronze_to_silver.ipynb` (date format transformations).
- **Notebook 3**: Final transformation (Silver to Gold) using `silver_to_gold.ipynb` (column name formatting).

### 4.3 Add Transformation to ADF Pipeline
- Create a linked service in ADF for **Databricks** using an access token stored in **Key Vault**.
- Add notebook activities for the transformations in the ADF pipeline.

![ADB Integration](https://drive.google.com/file/d/1Vfyl6IlLfc_7p33ozlwatmqJ-DCUnAOE/view?usp=drive_link)

---

## Step 5: Data Loading and Analysis using Azure Synapse
### 5.1 Create Gold Database in Synapse
- Create **gold_db** in **Azure Synapse** using **Serverless SQL Pool**.
- Query data from the **gold container** in **Data Lake**.

### 5.2 Create Views in Gold Database
- Create a pipeline in ADF to generate views in **gold_db**.
- Use a stored procedure to dynamically create views using the script `CreateViews_gold_db.sql`.

![Synapse Views](https://drive.google.com/file/d/1ZNkCeG-CWZJCjzApufiHYYLvEnm9YYyJ/view?usp=drive_link)

---

## Step 6: Reporting using Power BI
### 6.1 Create Interactive Dashboards
- Connect **Power BI Desktop** to the **serverless SQL pool** in Azure Synapse.
- Load the views from **gold_db**.
- Design interactive dashboards to visualize the data.

![interactive dashboards](https://drive.google.com/file/d/1llN2EIH07zhsmVa8yZmNixMpyOS-LfIZ/view?usp=drive_link)

---

## Conclusion
This README outlines the complete Azure Data Engineering project, from data ingestion and transformation to final reporting. Follow the steps to set up the pipeline and automate your data workflows.

---


