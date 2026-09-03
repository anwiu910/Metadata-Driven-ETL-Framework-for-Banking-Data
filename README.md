# Metadata-Driven ETL Framework for Banking Data

An end-to-end **Data Engineering pipeline built on Databricks** to ingest, transform, and analyze banking data from Azure SQL Server and CSV sources. The project implements a reusable **metadata-driven ETL framework**, Medallion Architecture, incremental data processing, and automated workflow orchestration.

---

## 📌 Project Overview

The goal of this project is to build a scalable banking data platform that integrates data from multiple sources and transforms it into business-ready datasets for analytics.

The pipeline supports different ingestion strategies based on metadata and processes data through **Bronze, Silver, and Gold layers**.

### Data Sources

* **Azure SQL Server**

  * Customers
  * Accounts
  * Transactions
  * Branches

* **CSV Files**

  * Credit Bureau Data
  * Payment Gateway Data

---

## 🏗️ Architecture

```text
                    DATA SOURCES
                         │
             ┌───────────┴───────────┐
             │                       │
       Azure SQL Server          CSV Files
             │                       │
           JDBC                 Auto Loader
             │                       │
             └───────────┬───────────┘
                         ↓
                  ┌─────────────┐
                  │   BRONZE    │
                  │ Raw Data    │
                  └──────┬──────┘
                         ↓
                  ┌─────────────┐
                  │   SILVER    │
                  │ Cleaned &   │
                  │ Refined Data│
                  └──────┬──────┘
                         ↓
                  ┌─────────────┐
                  │    GOLD     │
                  │ Business    │
                  │ Analytics   │
                  └──────┬──────┘
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
       Databricks Dashboards      Genie
              │                     │
              └──────────┬──────────┘
                         ↓
                  Business Users
```

The framework is supported by **metadata tables, watermarks, audit logging, Databricks Jobs, and Unity Catalog**.

---

## 🛠️ Technologies Used

* **Databricks**
* **PySpark**
* **Python**
* **SQL**
* **Delta Lake**
* **Azure SQL Server**
* **Databricks Auto Loader**
* **JDBC**
* **Unity Catalog**
* **Databricks Jobs**
* **Databricks SQL Dashboards**
* **Databricks Genie**

---

## ⚙️ Key Features

### 1. Metadata-Driven ETL

Instead of creating separate ingestion logic for every source table, the framework uses metadata to determine how each table should be processed.

Metadata controls parameters such as:

* Source table
* Target table
* Load strategy
* Primary key
* Watermark column
* Active/inactive status
* Processing configuration

This allows the same ETL framework to process multiple datasets with minimal code changes.

---

### 2. Multiple Load Strategies

The framework supports three ingestion strategies:

#### Full Load

Reloads the complete source dataset and replaces the target data.

```text
Source
  ↓
Read Full Dataset
  ↓
Target
```

#### Append Load

Adds newly received records to the existing dataset.

```text
Existing Data + New Records
           ↓
        Target
```

#### Merge Load

Updates existing records and inserts new records based on a primary key.

```text
If Key Exists → UPDATE
If Key Doesn't Exist → INSERT
```

---

## 🥉 Bronze Layer

The Bronze layer stores raw data ingested from the source systems.

### SQL Server

Data is extracted using **JDBC**.

```text
Azure SQL Server
       ↓
      JDBC
       ↓
Bronze Delta Tables
```

### CSV Sources

CSV files are ingested using **Databricks Auto Loader**.

```text
CSV Files
    ↓
Auto Loader
    ↓
Bronze Delta Tables
```

Auto Loader uses checkpoint and schema information to track processed files and support incremental file ingestion.

---

## 🥈 Silver Layer

The Silver layer contains refined and processed data.

Depending on the metadata configuration, data is processed using:

* Full Load
* Append
* Merge

For incrementally changing datasets, watermark-based filtering is used to identify newly added or updated records.

### Watermark-Based Processing

The framework stores the latest processed watermark and uses it during the next pipeline execution.

```text
Previous Watermark
        ↓
Filter Source Records
        ↓
Process New/Updated Records
        ↓
Update Watermark
```

This avoids repeatedly processing the entire source dataset.

---

## 🥇 Gold Layer

The Gold layer contains business-ready datasets created from the Silver layer.

### Branch Performance

Provides branch-level analytics using banking data such as:

* Customers
* Accounts
* Transactions
* Branch information

---

### Customer 360

Combines customer information with account, transaction, and credit-related data to provide a consolidated customer view.

Example analytics include:

* Customer information
* Account information
* Total balance
* Transaction activity
* Credit information
* Customer segmentation

---

### Daily Bank KPIs

Provides daily business metrics such as:

* Customer count
* Account count
* Transaction count
* Transaction amount
* Total balance
* Credit-related KPIs

---

### Transaction Channel Analysis

Combines transaction and payment gateway information to analyze:

* Transaction volume
* Successful transactions
* Failed transactions
* Payment gateways
* Device types
* Processing performance

---

## 🔄 Pipeline Orchestration

The complete workflow is orchestrated using **Databricks Jobs**.

```text
             Master Job
                 │
       ┌─────────┴─────────┐
       ↓                   ↓
 SQL Server Pipeline   CSV Pipeline
       │                   │
       └─────────┬─────────┘
                 ↓
           Silver Layer
                 ↓
            Gold Layer
                 ↓
           Data Refresh
                 ↓
       Dashboard / Genie
```

The workflow uses:

* Task dependencies
* Parallel task execution
* Retries
* Parameter passing
* Error handling
* Automated email alerts

---

## 📊 Audit Logging

The pipeline maintains audit information for monitoring and troubleshooting.

The audit framework tracks information such as:

* Run ID
* Table
* Layer
* Start time
* End time
* Processing status
* Record counts
* Error information

Example:

```text
Run ID | Table        | Layer  | Status
-----------------------------------------
1001   | Customers    | Silver | SUCCESS
1001   | Accounts     | Silver | SUCCESS
1001   | Transactions | Silver | FAILED
```

This makes pipeline execution traceable and simplifies failure investigation.

---

## 🔐 Security & Governance

The project uses **Unity Catalog** to organize and govern data assets.

Sensitive connection information is managed using **Databricks Secret Scope** rather than hardcoding credentials inside notebooks.

---

## 📈 Data Analytics

The Gold-layer datasets are exposed through **Databricks SQL dashboards** for business reporting.

The project also uses **Databricks Genie** to enable natural-language interaction with the prepared Gold datasets.

Example questions:

```text
Which branch has the highest total balance?

What is the daily transaction volume?

Which payment gateway has the highest failure rate?

What is the average credit score by customer segment?
```

---

## 🔁 Incremental Processing

The project demonstrates both initial and incremental processing.

### Initial Load

```text
Source
  ↓
Full Historical Data
  ↓
Bronze
  ↓
Silver
  ↓
Gold
```

### Incremental Load

```text
New / Updated Source Data
          ↓
   Watermark / Checkpoint
          ↓
        Bronze
          ↓
        Silver
          ↓
         Gold
```

This reduces unnecessary reprocessing and makes the pipeline more suitable for recurring execution.

---

## 📁 Project Structure

```text
├── notebooks/
│   ├── source_to_bronze/
│   ├── bronze_to_silver/
│   ├── silver_to_gold/
│   ├── metadata/
│   └── notifications/
│
├── sql/
│   ├── metadata_tables.sql
│   ├── audit_tables.sql
│   └── gold_transformations.sql
│
├── data/
│   ├── credit_bureau/
│   └── payment_gateway/
│
├── dashboards/
│
└── README.md
```

---

## 🚀 End-to-End Workflow

1. Configure source and processing metadata.
2. Read active tables from metadata.
3. Ingest SQL Server data using JDBC.
4. Ingest CSV data using Auto Loader.
5. Store raw data in the Bronze layer.
6. Apply Full, Append, or Merge strategies.
7. Use watermarks for incremental processing.
8. Store refined data in Silver Delta tables.
9. Create business-ready Gold datasets.
10. Record pipeline execution details in the audit tables.
11. Orchestrate the workflow using Databricks Jobs.
12. Refresh dashboards and make analytics available through Genie.
13. Send pipeline execution notifications.

---

## 🎯 Business Outcomes

The platform provides:

* Reusable metadata-driven ingestion
* Incremental and scalable data processing
* Centralized banking analytics
* Traceable pipeline execution
* Automated workflow orchestration
* Business-ready datasets for reporting
* Natural-language analytics through Genie

---

## 💡 Key Data Engineering Concepts Demonstrated

* Metadata-Driven ETL
* Medallion Architecture
* Incremental Data Processing
* Watermarking
* Delta Lake MERGE
* Auto Loader
* JDBC Data Ingestion
* PySpark Transformations
* Databricks Jobs
* Pipeline Monitoring and Auditing
* Data Governance
* Business Analytics
