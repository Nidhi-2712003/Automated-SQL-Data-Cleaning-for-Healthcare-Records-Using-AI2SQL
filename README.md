# Healthcare Data Cleaning & Automation Project (AI2SQL)

## Project Title
**Automated SQL Data Cleaning for Healthcare Records Using AI2SQL**

## Project Description
This project demonstrates a complete SQL automation workflow for cleaning, standardizing, and analyzing synthetic healthcare data using the AI2SQL tool with PostgreSQL. The project showcases best practices in healthcare data engineering — including raw table import, iterative cleaning, fixing AI2SQL prompt syntax issues, and exploring trends/patterns in the cleaned dataset. All steps are explained for reproducibility and learning, and the code is ready to use for similar data automation tasks.  

---

## Introduction
Healthcare datasets often contain duplicates, messy text, outlier records, and missing values. Automating data cleaning with natural language (via AI2SQL) and applying robust SQL fixes adds speed and consistency to healthcare analytics workflows.

---

## Data Source

**File:** `healthcare_dataset.csv`  
**Source:** Publicly available on GitHub  
**Original Repository:** https://github.com/imranbdcse/healthcaredatasets (DataScience branch)

This dataset contains **synthetic healthcare records** (demographics, diagnoses, hospital details, billing, and test results) created for learning, research, and demonstration purposes. It does **not** contain real patient information.

The dataset is used here **solely for educational, analytical, and non-commercial purposes** as part of an AI2SQL and data-cleaning demonstration project. No ownership of the dataset is claimed. If you are the original creator or copyright holder and wish this dataset reference to be modified or removed, please open an issue in this repository, and the request will be addressed promptly.

---

## Project Workflow
- Import CSV data into PostgreSQL using pgAdmin or CLI.  
<img width="940" height="588" alt="image" src="https://github.com/user-attachments/assets/b1fb84ba-b343-407e-8f4c-b7076b406b11" />
- Inspect and visualize the raw table structure and sample records. 
<img width="940" height="588" alt="image" src="https://github.com/user-attachments/assets/3e634dd6-5c87-4b21-a64d-9ed004744690" />
- Use AI2SQL prompts to generate SQL for trend/pattern analysis in the cleaned data and Apply sequential cleaning queries in AI2SQL (each adapted for robust PostgreSQL syntax). 
<img width="925" height="501" alt="image" src="https://github.com/user-attachments/assets/022392d8-ea95-4203-abde-8e03713f4865" />
<img width="935" height="466" alt="image" src="https://github.com/user-attachments/assets/aa99834f-1cb0-4824-b39a-541cb9bba372" />
<img width="932" height="473" alt="image" src="https://github.com/user-attachments/assets/4b20e33e-17d2-442e-86cd-d690eb81eef7" />

## Key Fixes & Learning (Since query suggested by AI2SQL has syntax issues)

- Corrected AI2SQL-generated SQL to match **PostgreSQL** syntax (e.g., using `DELETE USING` for de-duplication).  
- Added robustness to cleaning steps:  
  - Null checks  
  - Text normalization  
  - Type consistency  
  - Outlier handling  
- Applied PostgreSQL text functions for standardization (e.g., `INITCAP`, `UPPER`, `TRIM`).  
- Documented each step for reproducibility and clarity.  
- Ensured all cleaning logic is idempotent and reusable.

## Step-by-Step Data Cleaning in Postgree SQL

```sql

1. Remove Nulls & Empty Required Fields

DELETE FROM healthcaredata
WHERE TRIM(COALESCE("Name", '')) = ''
   OR "Age" IS NULL
   OR TRIM(COALESCE("Gender", '')) = ''
   OR TRIM(COALESCE("Blood Type", '')) = ''
   OR TRIM(COALESCE("Medical Condition", '')) = ''
   OR TRIM(COALESCE("Hospital", '')) = ''
   OR TRIM(COALESCE("Insurance Provider", '')) = ''
   OR "Billing Amount" IS NULL
   OR TRIM(COALESCE("Room Number", '')) = ''
   OR TRIM(COALESCE("Admission Type", '')) = ''
   OR "Date of Admission" IS NULL
   OR "Discharge Date" IS NULL
   OR TRIM(COALESCE("Medication", '')) = ''
   OR TRIM(COALESCE("Test Results", '')) = '';

---

2. Remove Exact Duplicates
DELETE FROM healthcaredata a
USING healthcaredata b
WHERE a.ctid <> b.ctid
  AND COALESCE(a."Name", '') = COALESCE(b."Name", '')
  AND COALESCE(a."Age", '') = COALESCE(b."Age", '')
  AND COALESCE(a."Gender", '') = COALESCE(b."Gender", '')
  AND COALESCE(a."Blood Type", '') = COALESCE(b."Blood Type", '')
  AND COALESCE(a."Medical Condition", '') = COALESCE(b."Medical Condition", '')
  AND COALESCE(a."Hospital", '') = COALESCE(b."Hospital", '')
  AND COALESCE(a."Insurance Provider", '') = COALESCE(b."Insurance Provider", '')
  AND COALESCE(a."Billing Amount", '') = COALESCE(b."Billing Amount", '')
  AND COALESCE(a."Room Number", '') = COALESCE(b."Room Number", '')
  AND COALESCE(a."Admission Type", '') = COALESCE(b."Admission Type", '')
  AND COALESCE(a."Date of Admission", '') = COALESCE(b."Date of Admission", '')
  AND COALESCE(a."Discharge Date", '') = COALESCE(b."Discharge Date", '')
  AND COALESCE(a."Medication", '') = COALESCE(b."Medication", '')
  AND COALESCE(a."Test Results", '') = COALESCE(b."Test Results", '');

---

  3. Remove Outliers & Logic Errors
DELETE FROM healthcaredata
WHERE "Age" < 0 OR "Age" > 120
   OR ("Billing Amount" IS NOT NULL AND "Billing Amount" < 0)
   OR ("Date of Admission" > "Discharge Date");

---

4. Standardize Gender
UPDATE healthcaredata
SET "Gender" = CASE
  WHEN LOWER(TRIM("Gender")) IN ('male', 'm') THEN 'Male'
  WHEN LOWER(TRIM("Gender")) IN ('female', 'f') THEN 'Female'
  ELSE 'Other'
END
WHERE "Gender" IS NOT NULL;

---

5. Normalize Blood Type
UPDATE healthcaredata
SET "Blood Type" = UPPER(REPLACE(TRIM("Blood Type"), ' ', ''))
WHERE "Blood Type" IS NOT NULL;

UPDATE healthcaredata
SET "Blood Type" = NULL
WHERE "Blood Type" NOT IN ('A', 'A-', 'B', 'B-', 'AB', 'AB-', 'O', 'O-')
   OR "Blood Type" IS NULL;

---

6. Title Case for Text Columns
UPDATE healthcaredata
SET "Name" = INITCAP(TRIM("Name")),
    "Medical Condition" = INITCAP(TRIM("Medical Condition")),
    "Hospital" = INITCAP(TRIM("Hospital")),
    "Insurance Provider" = INITCAP(TRIM("Insurance Provider")),
    "Admission Type" = INITCAP(TRIM("Admission Type")),
    "Medication" = INITCAP(TRIM("Medication")),
    "Test Results" = INITCAP(TRIM("Test Results"));

---
```
---

## Cleaned file

[Healthcare_dataset_AI2SQL.xlsx](https://github.com/user-attachments/files/23561228/Healthcare_dataset_AI2SQL.xlsx)

---

## Final Steps

- Use AI2SQL prompts to generate SQL for trend/pattern analysis in the cleaned data. 
<img width="922" height="471" alt="image" src="https://github.com/user-attachments/assets/8a877cb8-ef15-40d1-ba4b-f49cfc82cdea" />
<img width="940" height="470" alt="image" src="https://github.com/user-attachments/assets/2159e25f-22d5-42c6-a622-3f741adeeae1" />
<img width="926" height="469" alt="image" src="https://github.com/user-attachments/assets/c3fdb240-8a15-482a-83c2-48b18e96a708" />

## Results

- Data was successfully cleaned, deduplicated, and normalized using SQL generated and corrected through AI2SQL.  
- Key metrics, trends, and patterns can now be extracted from the cleaned dataset.  
- Screenshots provide a visual walkthrough of the transformation steps and final analytical outputs.

---
