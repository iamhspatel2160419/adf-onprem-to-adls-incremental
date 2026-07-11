# adf-onprem-to-adls-incremental
Implemented an Azure Data Factory incremental data load solution using the watermark pattern. The pipeline extracts data from an on-premises SQL Server through Self-hosted Integration Runtime (SHIR), loads it into ADLS Gen2 in Parquet format, updates the watermark table after a successful load, and supports dynamic processing of multiple tables.
