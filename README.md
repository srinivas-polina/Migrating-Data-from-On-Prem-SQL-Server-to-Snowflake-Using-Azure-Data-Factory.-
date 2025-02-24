# 🚀 Migrating Data from On-Prem SQL Server to Snowflake Using Azure Data Factory (ADF) & Azure Data Lake Storage Gen2 (ADLS2)

## 📌 Overview
This project demonstrates how to migrate large-scale data from an **On-Prem SQL Server** to **Snowflake** using **Azure Data Factory (ADF)** and **Azure Data Lake Storage Gen2 (ADLS2)**. The architecture ensures secure and efficient data transfer using a **Hybrid Data Pipeline**.

## 🏗 Architecture

- **Source:** On-Prem SQL Server 🏢
- **Integration Layer:** Azure Data Factory (ADF) with **Self-Hosted Integration Runtime (SHIR) 🛠**
- **Staging:** Azure Data Lake Storage Gen2 (ADLS2) 📂
- **Destination:** Snowflake ❄️

---
## 🔧 Migration Process
### 1️⃣ Extract Data from On-Prem SQL Server
- Install & configure **Self-Hosted Integration Runtime (SHIR)** in Azure Data Factory.
- Create a **Linked Service** in ADF for **SQL Server**.
- Use **Lookup & Copy Data activities** to extract large datasets.
- Apply **partitioning & incremental loading** techniques for efficiency.

### 2️⃣ Store Data in Azure Data Lake Storage Gen2 (ADLS2)
- Transform and store extracted data as **CSV files** in ADLS2.
- Organize data using **hierarchical folder structures** for better management.

### 3️⃣ Ingest Data into Snowflake
- Create an **external stage** in Snowflake pointing to ADLS2.
- Set up **Storage Integrations** in Snowflake for secure access.
- Use the **COPY INTO** command to bulk load data into Snowflake tables.

---
## 🛠 Setting Up Self-Hosted Integration Runtime (SHIR) in ADF
To connect on-prem SQL Server with Azure securely:

1️⃣ **Download & Install SHIR** from the Azure portal.
2️⃣ **Register SHIR** in Azure Data Factory.
3️⃣ **Create a Linked Service** in ADF using SHIR for SQL Server.
4️⃣ **Use SHIR** in Copy Data activities to extract and move data.

---
## ⚡ Key Features & Best Practices
✅ **Secure Data Movement** – SHIR ensures secure connectivity between on-prem and Azure.
✅ **Optimized Performance** – Partitioning & incremental loading reduce processing time.
✅ **Snowflake Storage Integration** – Secure authentication between Snowflake & ADLS2.
✅ **Efficient Data Loading** – COPY INTO command speeds up bulk data ingestion.

---
## 🚀 Commands & Code Snippets
### 🎯 Create an External Stage in Snowflake:
```sql
CREATE OR REPLACE STAGE my_stage
URL='azure://<storage-account>.dfs.core.windows.net/<container>'
STORAGE_INTEGRATION = my_storage_integration;
```
### 🎯 Load Data into Snowflake Using COPY INTO:
```sql
COPY INTO my_table
FROM @my_stage/path/to/files/
FILE_FORMAT = (TYPE = CSV FIELD_OPTIONALLY_ENCLOSED_BY='"');
```
### 🎯 code used for LOOKUP tables in ADF and Snowflake Script:
```sql
select TABLE_SCHEMA,TABLE_NAME from AdventureWorks2022.INFORMATION_SCHEMA.TABLES where TABLE_TYPE='BASE TABLE';
---

### 🎯 code used for snowflake script:
USE MY_DATABASE.PUBLIC;

CREATE or REPLACE TABLE MY_DATABASE.PUBLIC.@{item().TABLE_NAME}
  USING TEMPLATE (
    SELECT ARRAY_AGG(OBJECT_CONSTRUCT(*))
      FROM TABLE(
        INFER_SCHEMA(
          LOCATION=>'@{concat('@MIGRATIONS/migration_data/',item().TABLE_SCHEMA,'_',item().TABLE_NAME,'.CSV')}',
          FILE_FORMAT=>'my_csv_format'
        )
      ));

COPY INTO CULTURE_TEST FROM '@{concat('@MIGRATIONS/migration_data/',item().TABLE_SCHEMA,'_',item().TABLE_NAME,'.CSV')}'
            FILE_FORMAT = (
            FORMAT_NAME= 'my_csv_format'
            )
            MATCH_BY_COLUMN_NAME=CASE_INSENSITIVE;
```
## 🏗 Prerequisites
- **Azure Subscription** with ADF & ADLS2 configured.
- **Snowflake Account** with Storage Integration enabled.
- **On-Prem SQL Server** with firewall rules allowing SHIR connectivity.
- **Self-Hosted Integration Runtime (SHIR)** installed & registered in ADF.

---
## 📚 References
- [Azure Data Factory Documentation](https://learn.microsoft.com/en-us/azure/data-factory/)
- [Snowflake External Stages](https://docs.snowflake.com/en/user-guide/data-load-external-tutorial.html)
- [Self-Hosted Integration Runtime Setup](https://learn.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime)

---
## 🚀 Future Enhancements
- Automate incremental data loads using **Watermarking & Change Data Capture (CDC)**.
- Implement **Delta Lake** for optimized storage & processing.
- Integrate **Data Quality Checks** before ingestion.

---
## 💡 Conclusion
This project successfully demonstrates a hybrid cloud **data pipeline**, ensuring **secure, optimized, and scalable** data migration from **on-prem SQL Server to Snowflake** using **Azure Data Factory & ADLS2**. 🚀

