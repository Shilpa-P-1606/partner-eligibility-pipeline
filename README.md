# Partner-Eligibility-Pipeline
This project implements a configuration driven data ingestion pipeline that processes partner files with different formats and transforms them into a standardized output schema. The goal is to onboard new partners without changing core processing logic by relying only on configuration updates.

###Problem Statement
Different partners send data using different delimiters, column names, and formats. This pipeline ingests those heterogeneous files, standardizes them into a common schema, applies data quality validations, and separates valid and invalid records for downstream consumption.

### Architecture Overview
The pipeline follows a layered approach. Raw partner files are ingested into a raw table. The raw data is then parsed using partner specific configurations. Parsed data is standardized into a common schema, validated, and written to final output tables. Invalid records are captured separately with clear error reasons.

**High level flow**

-  Raw file ingestion  
-  Parsing using partner configuration  
-  Standardization and transformations  
- Validation and error tagging  
- Write valid records to final table  
- Write invalid records to error table  

### Input Files and Partner Configuration
Each partner is onboarded using a configuration file or object. No code changes are required to support a new partner.

**Each partner configuration defines**

- Partner code  
- File delimiter  
- Header presence  
- Column mappings from partner column to standard field  
- Optional default values  

**Standard Output Schema**

The final standardized dataset follows the schema below.

- **external_id** mapped from partner unique identifier  
- **first_name** converted to title case  
- **last_name** converted to title case  
- **dob** formatted as YYYY MM DD  
- **email** converted to lowercase  
- **phone** formatted as XXX XXX XXXX  
- **partner_code** hardcoded per partner  

### Validations and Error Handling

The pipeline enforces multiple data quality checks.

- Records without **external_id** are marked invalid  
- Records with missing or invalid **dob** are marked invalid  
- Records with missing or invalid **phone number** are marked invalid 

An **error_reason column** is generated for invalid rows. Multiple validation failures are concatenated into a single error message. Invalid records are written to a separate error table for auditing and analysis.

### How to Run the Pipeline
This pipeline was developed and executed using **Databricks Free Edition** for demonstration purposes.

**Prerequisites**
- Databricks Free Tier account  
- Access to a SQL Warehouse or Serverless SQL endpoint  
- Sample partner input files uploaded manually  

No external cloud storage or production credentials are required.

**Execution Steps**

1. Upload partner files manually into Databricks or register them as tables  
2. Create a raw table containing the partner data  
3. Verify the partner configuration object is defined in the notebook  
4. Run the notebook cells sequentially to  
   - Parse raw data using the configured delimiter  
   - Map partner columns to the standard schema  
   - Apply transformations and validations  
   - Generate valid and error datasets  
5. Review results directly in the Databricks results grid  

**Output Verification**
- Valid records are written to a standardized output table  
- Invalid records are written to a separate error table with an error_reason column  
- Record counts and sample rows are reviewed using SELECT queries 

###Configuration Examples
Partner specific behavior is controlled using an in notebook configuration object.

**Example Configuration Structure**
```python
partner_config = {
    "partner_code": "ACME",
    "delimiter": "|",
    "has_header": True,
    "column_mapping": {
        "MBI": "external_id",
        "FNAME": "first_name",
        "LNAME": "last_name",
        "DOB": "dob",
        "EMAIL": "email",
        "PHONE": "phone"
    }
}
```
Different partners can define different delimiters and column names using the same structure.

**Adding a New Partner**

This project supports onboarding a new partner using configuration only, without modifying the pipeline logic.

1. Upload the new partner file manually into Databricks or create a temporary table from the file  
2. Add a new entry to the in notebook partner configuration object specifying  
   - Partner code  
   - File delimiter  
   - Column mappings from partner columns to standard fields  
3. Run the notebook cells for the new partner configuration  
4. Review the results in the standardized output and error tables directly in Databricks  

✅ **_No changes to the core transformation or validation code_** are required.

### Testing and Validation
Data quality was validated using record counts, sample inspections, and validation rule coverage. Sample partner files were used to ensure correct parsing, transformation, and error handling.

**Raw Input Validation**

The **ACME** raw partner **Text file** was ingested as a single column named `value` into the raw table.



This dataset intentionally includes both valid and invalid records to validate parsing, transformation, and error handling logic.

The **BetterCare** raw partner **CSV file** was ingested into a structured raw table with individual columns such as subscriber_id, first_name, last_name, date_of_birth, email, and phone.

<img width="700" height="286" alt="image" src="https://github.com/user-attachments/assets/29b30619-1024-4282-b5fd-8ed4f4e3f4d7" />


This dataset includes only valid records to validate transformation, standardization, and error handling logic within the pipeline.


## Repository Structure

- notebooks contains pipeline notebooks  
- data contains sample input files   

