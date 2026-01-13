# Partner-Eligibility-Pipeline 🚀

This project demonstrates a configuration driven data ingestion pipeline built and executed using **Databricks Free Edition notebooks**.  
The pipeline processes partner files with different formats and transforms them into a standardized output schema.  
The primary goal is to show how new partners can be onboarded using configuration only, without modifying core transformation logic.

---

### Problem Statement  🧩

Different partners provide eligibility data using different delimiters, column names, and file structures.  
This project ingests those heterogeneous partner files, standardizes them into a unified schema, applies data quality validations, and separates valid and invalid records for easy analysis.

---

### Architecture Overview 🏗️

The pipeline follows a simple layered design implemented entirely in Databricks notebooks.

**High level flow**

-  Raw file ingestion  
-  Parsing using partner configuration  
-  Standardization and transformations  
- Validation and error tagging  
- Write valid records to final table  
- Write invalid records to error table

This approach keeps the notebook logic generic and reusable across partners.

---

### Input Files and Partner Configuration ⚙️

Each partner is onboarded using an **in notebook configuration object**.  
No production pipelines, jobs, or orchestration tools are used.  
All logic is executed interactively within Databricks Free Edition notebooks.

**Each partner configuration defines**

- Partner code  
- File delimiter  
- Header presence  
- Column mappings from partner column to standard field  
- Optional default values  
---

**Standard Output Schema**  📊

The final standardized dataset follows a common schema regardless of partner format.

The standardized fields include

- **external_id** mapped from partner unique identifier  
- **first_name** converted to title case  
- **last_name** converted to title case  
- **dob** formatted as YYYY MM DD  
- **email** converted to lowercase  
- **phone** formatted as XXX XXX XXXX  
- **partner_code** hardcoded per partner  
---

### Validations and Error Handling  ✅

The pipeline enforces multiple data quality checks.

The following rules are enforced

- Records without **external_id** are marked invalid  
- Records with missing or invalid **dob** are marked invalid  
- Records with missing or invalid **phone number** are marked invalid 

An **error_reason** column is generated for invalid rows.  
Multiple validation failures are concatenated into a single descriptive message.  
Invalid records are written to a separate error table for review and debugging.

---


### How to Run the Pipeline 🧪
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

---

### Configuration Examples 🛠️

Partner specific behavior is controlled using an **in notebook configuration dictionary**.

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

---

### Adding a New Partner ➕

This project supports onboarding a new partner using configuration only, without modifying the pipeline logic.

1. Upload the new partner file manually into Databricks or create a temporary table from the file  
2. Add a new entry to the in notebook partner configuration object specifying  
   - Partner code  
   - File delimiter  
   - Column mappings from partner columns to standard fields
3. Run the notebook cells for the new partner configuration  
4. Review the results in the standardized output and error tables directly in Databricks  

✨ **_No changes to the core transformation or validation code_** are required.

---

## Testing and Validation  🔍

Data quality was validated using record counts, sample inspections, and validation rule coverage.  
Sample partner files were intentionally designed to test both valid and invalid scenarios.

---

### Raw Input Validation

1. The **ACME** raw partner **Text file** was ingested as a single column named `value` into the raw table.

   <br>
   <img width="450" height="300" alt="image" src="https://github.com/user-attachments/assets/b4e826a8-cc6e-46f5-8f3e-8fd2945052e8" />
   <br>
   <br>

   This dataset intentionally includes both valid and invalid records to validate parsing, transformation, and error handling logic.

2. The **BetterCare** raw partner **CSV file** was ingested into a structured raw table with individual columns such as subscriber_id, first_name, last_name, date_of_birth, email, and phone.

   <br>
   <img width="700" height="286" alt="image" src="https://github.com/user-attachments/assets/29b30619-1024-4282-b5fd-8ed4f4e3f4d7" />
   <br>
   <br>

   This dataset includes only valid records to validate transformation, standardization, and error handling logic within the pipeline.
   
   ---
   
### Final Standardized Output 📈

The final output of the pipeline is written to a unified table named `unified_members`.

   This table contains validated and standardized member records from multiple partners, combined into a single schema.

   <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/89924127-640f-4328-92f7-c4f8e4803512" />
   <br>

  Each row represents a single valid member record ready for downstream consumption.

 ---

### Error Output Table  🚨

Invalid records identified during validation are written to a separate table named `unified_members_errors`.

This table preserves the original data along with detailed error information.

   <br>
   <img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/85b00380-6448-48db-8f48-437e76413313" />
    <br>

   ---

## Repository Structure 📁

- Notebooks contains pipeline notebooks  
- Data contains sample input files
- Output a single, unified dataset. 
- ReadME File 

---

✨ This project showcases practical data engineering concepts using Databricks Free Edition notebooks and focuses on clarity, configurability, and data quality.
