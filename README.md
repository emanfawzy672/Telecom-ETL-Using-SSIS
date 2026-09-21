# Telecom-ETL-Using-SSIS
-----------------------------------------------------------------------------------------------------------------------
This ETL solution leverages SQL Server Integration Services (SSIS) to extract transaction data from flat files, transform and clean the data, and load it into a SQL Server database. This setup is specifically designed to handle telecom transaction data, process errors, and perform audits.

Table of Contents
Overview
Database Tables
Variables
SSIS Package Workflow
Auditing
Usage
License
Overview
The solution extracts data from flat files (CSV format), processes both valid and invalid records, and loads them into corresponding database tables. The ETL process has several components:

Fact Transaction Table - Stores valid records.
Error Tables - Stores invalid records for troubleshooting.
Dimension Table - Contains reference information to enrich transactions.
This setup includes looping through multiple files, validating data types, checking for null values, and error handling.

Database Tables
1. Fact_Transaction Table
This table stores valid transactions that meet the schema requirements.
Each record comes from a source flat file and is transformed if needed before insertion.
2. Err_Destination_Output Table
This table stores transactions that have errors in the Fact_Transaction table, such as null values in critical fields.
3. Dim_IMSI_Reference Table
This dimension table includes subscriber_id reference data, used to enrich transactions in Fact_Transaction.
Helps check whether a subscriber_id is null.
Contains logic to derive values for TAC and SNR fields based on the imsi column.
4. Err_Source_Output Table
Stores records from the source flat files that have incorrect data types or are incompatible with the Fact_Transaction table structure.
Variables
File Handling Variables
ff_src_file_name - Stores the name of the current source file.
ff_src_folder_path - Defines the folder path containing source files.
ff_src_file_extension - Specifies the file extension (e.g., .csv).
ff_src_full_path - The full path combining folder and filename for the source file.
Archive Variables
ff_dest_archive - Destination folder path for moving or copying processed files.
move_copy - Defines whether the file should be copied or moved after processing.
Auditing Variables
audit_id - Unique identifier for each row in the database during loading.
batch_id - Stores the maximum batch_id + 1 from dim_audit table, representing each new file batch.
rc_extracted_processed - Row count of records extracted and processed.
rc_extracted_error - Row count of records with errors.
rc_dest_error - Row count of error rows in src_destination_output.
rc_pre_insert - Row count of valid records ready for insertion into fact_transaction.
SSIS Package Workflow
ForEach Loop Container

Iterates through all files in the specified source folder.
For each file, data is extracted, transformed, validated, and then loaded into appropriate tables.
File Processing

Validates each record’s data type and field requirements before loading into Fact_Transaction.
Moves invalid records to either Err_Destination_Output or Err_Source_Output.
Data Transformation

Uses the imsi column to derive TAC (first 8 characters) and SNR (next 6 characters) before loading data.
File Archiving

Based on the move_copy variable, processed files are either moved or copied from the source folder to the destination archive folder.
Auditing
Each file processing run includes an audit process to track and log various stages of the ETL:

audit_id logs each row of data loaded.
batch_id helps identify individual file batches and increments with each new file.
Row count variables (rc_extracted_processed, rc_extracted_error, rc_dest_error, rc_pre_insert) track records at different stages of ETL to monitor and ensure data quality.
Usage
Place source flat files in the specified ff_src_folder_path.
Set SSIS variable values, such as ff_dest_archive and move_copy.
Execute the SSIS package to start the ETL process. Processed files will be moved or copied based on the move_copy variable.
Review the logs in dim_audit table to verify counts and ensure data accuracy.
License
This project is licensed under the MIT License. See LICENSE for details.
