# Azure Data Factory – On-Prem SQL Server to ADLS Gen2 Incremental Load

## Overview

This project demonstrates an end-to-end Azure Data Factory (ADF) solution for loading data from an On-Premises SQL Server into Azure Data Lake Storage Gen2 (ADLS Gen2) using a watermark-based incremental loading approach.

The solution supports both full and incremental loads, dynamically generates SQL queries, writes data in Parquet format, and updates the watermark value after each successful execution. A Parent–Child pipeline architecture is used to process multiple tables dynamically.

---

## Solution Architecture

```text
On-Prem SQL Server
        │
        │
Self-hosted Integration Runtime (SHIR)
        │
        ▼
Azure Data Factory
        │
        ├── Parent Pipeline
        │       │
        │       ├── Lookup Metadata
        │       ├── ForEach Table
        │       └── Execute Child Pipeline
        │
        ▼
Child Pipeline
        │
        ├── Read Watermark
        ├── Generate Dynamic SQL
        ├── Copy Incremental Data
        └── Update Watermark
        │
        ▼
Azure Data Lake Storage Gen2
        │
        ▼
Parquet Files
```

---

## Project Features

* Full Load and Incremental Load
* Watermark-based Incremental Processing
* Dynamic SQL Query Generation
* Parent–Child Pipeline Architecture
* Parameterized Pipelines and Datasets
* Dynamic Folder and File Name Generation
* Parquet File Output
* Self-hosted Integration Runtime (SHIR)
* Metadata-Driven Processing
* GitHub Source Control

---

## Pipelines

### PL_PARENT_ORCHESTRATION

Responsibilities:

* Reads source table metadata
* Executes child pipeline dynamically
* Passes table-specific parameters
* Supports multiple table processing

### PL_CHILD_INCREMENTAL_LOAD

Responsibilities:

* Reads the last watermark value
* Creates dynamic SQL query
* Performs Full/Incremental load
* Writes data into ADLS Gen2 in Parquet format

### PL_CHILD_STORED_PROCEDURE

Responsibilities:

* Calls SQL Stored Procedure
* Updates watermark table after successful load

---

## Datasets

| Dataset             | Purpose                      |
| ------------------- | ---------------------------- |
| DS_SQL_Source       | Source SQL Server table      |
| DS_SQLTable_Parquet | Sink ADLS Gen2 Parquet files |
| DS_LKUP_MAX_VALUE   | Lookup watermark value       |

---

## Linked Services

| Linked Service      | Purpose                      |
| ------------------- | ---------------------------- |
| LS_AzureSQL         | SQL Server Connection        |
| LS_SQLTable_PARQUET | ADLS Gen2 Storage Connection |

---

## Integration Runtime

* Self-hosted Integration Runtime (SHIR)

Used to securely connect the On-Premises SQL Server with Azure Data Factory.

---

## Incremental Load Workflow

1. Parent pipeline retrieves table metadata.
2. Child pipeline receives table parameters.
3. Lookup activity reads the latest watermark value.
4. Dynamic SQL query is generated.
5. New records are copied from SQL Server.
6. Data is stored in ADLS Gen2 as Parquet files.
7. Stored Procedure updates the watermark value.

---

## Dynamic Expressions

### Dynamic SQL

```adf
@concat(
'SELECT * FROM ',
pipeline().parameters.TableSchema,
'.',
pipeline().parameters.TableName,
if(
equals(pipeline().parameters.LastLoadValue,null),
'',
concat(
' WHERE ',
pipeline().parameters.WatermarkColumn,
' > ''',
pipeline().parameters.LastLoadValue,
''''
)
)
)
```

### Dynamic Folder

```adf
@concat(
dataset().Table_Name,
'/',
formatDateTime(utcNow(),'yyyy/MM/dd')
)
```

### Dynamic File Name

```adf
@concat(
dataset().Table_Name,
'_',
formatDateTime(utcNow(),'yyyyMMddHHmmss'),
'.parquet'
)
```

---

## Repository Structure

```text
.
├── dataset
├── factory
├── integrationRuntime
├── linkedService
├── pipeline
├── publish_config.json
├── LICENSE
└── README.md
```

---

## Technologies Used

* Azure Data Factory
* Azure Data Lake Storage Gen2
* SQL Server
* Self-hosted Integration Runtime
* SQL Stored Procedures
* Dynamic Content Expressions
* Parquet
* GitHub

---

## Learning Outcomes

This project helped me gain hands-on experience with:

* Azure Data Factory Pipelines
* Incremental Data Loading
* Watermark Pattern
* Parent–Child Pipeline Design
* Dynamic Expressions
* Self-hosted Integration Runtime
* ADLS Gen2
* Parquet File Format
* Metadata-Driven ETL
* GitHub Version Control

---

## Author

**Hardik Patel**

Azure Data Engineering | Azure Data Factory | SQL Server | ADLS Gen2 | ETL | Data Engineering
