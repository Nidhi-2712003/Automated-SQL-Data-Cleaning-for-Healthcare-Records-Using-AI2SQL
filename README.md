# Automated-SQL-Data-Cleaning-for-Healthcare-Records-Using-AI2SQL

Healthcare Data Cleaning & Automation Project (AI2SQL)
Project Title
Automated SQL Data Cleaning for Healthcare Records Using AI2SQL

Project Description
This project demonstrates a complete SQL automation workflow for cleaning, standardizing, and analyzing synthetic healthcare data using the AI2SQL tool with PostgreSQL. The project showcases best practices in healthcare data engineering — including raw table import, iterative cleaning, fixing AI2SQL prompt syntax issues, and exploring trends/patterns in the cleaned dataset. All steps are explained for reproducibility and learning, and the code is ready to use for similar data automation tasks.

Table of Contents
Introduction

Data Source

Project Workflow

Screenshots

Step-by-Step Data Cleaning

Trend Analysis Queries (AI2SQL)

Results

Key Fixes & Learning

How to Reproduce

Introduction
Healthcare datasets often contain duplicates, messy text, outlier records, and missing values. Automating data cleaning with natural language (via AI2SQL) and applying robust SQL fixes adds speed and consistency to healthcare analytics workflows.

Data Source
File: healthcare.csv

Origin: imranbdcse/healthcaredatasets

Domain: Synthetic patient EHR, including demographics, diagnosis, billing, hospital details, and test results.

Project Workflow
Import CSV data into PostgreSQL using pgAdmin or CLI.

Inspect and visualize the raw table structure and sample records.

Apply sequential cleaning queries in AI2SQL (each adapted for robust PostgreSQL syntax).

Document cleaning steps and results, including screenshots.

Use AI2SQL prompts to generate SQL for trend/pattern analysis in the cleaned data.

Share final output and insights.

Screenshots
Insert your raw and cleaned table screenshots below. For GitHub markdown, drag-and-drop images into this README file in the GitHub editor, or use syntax like:

text
![Raw Table](images/raw_table.png)
![Cleaning Steps](images/cleaning_steps.png)
![Cleaned Table](images/cleaned_table.png)
![Trend Analysis Output](images/trend_analysis.png)
Note: Replace image filenames with your actual file names or use drag-and-drop as needed.

Step-by-Step Data Cleaning
All table/column names use underscores and lowercase. Adjust only if your schema uses different names.

1. Remove Nulls & Empty Required Fields
sql
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
2. Remove Exact Duplicates
sql
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
3. Remove Outliers & Logic Errors
sql
DELETE FROM healthcaredata
WHERE "Age" < 0 OR "Age" > 120
   OR ("Billing Amount" IS NOT NULL AND "Billing Amount" < 0)
   OR ("Date of Admission" > "Discharge Date");
4. Standardize Gender
sql
UPDATE healthcaredata
SET "Gender" = CASE
  WHEN LOWER(TRIM("Gender")) IN ('male', 'm') THEN 'Male'
  WHEN LOWER(TRIM("Gender")) IN ('female', 'f') THEN 'Female'
  ELSE 'Other'
END
WHERE "Gender" IS NOT NULL;
5. Normalize Blood Type
sql
UPDATE healthcaredata
SET "Blood Type" = UPPER(REPLACE(TRIM("Blood Type"), ' ', ''))
WHERE "Blood Type" IS NOT NULL;

UPDATE healthcaredata
SET "Blood Type" = NULL
WHERE "Blood Type" NOT IN ('A', 'A-', 'B', 'B-', 'AB', 'AB-', 'O', 'O-')
   OR "Blood Type" IS NULL;
6. Title Case for Text Columns
sql
UPDATE healthcaredata
SET "Name" = INITCAP(TRIM("Name")),
    "Medical Condition" = INITCAP(TRIM("Medical Condition")),
    "Hospital" = INITCAP(TRIM("Hospital")),
    "Insurance Provider" = INITCAP(TRIM("Insurance Provider")),
    "Admission Type" = INITCAP(TRIM("Admission Type")),
    "Medication" = INITCAP(TRIM("Medication")),
    "Test Results" = INITCAP(TRIM("Test Results"));
Trend Analysis Queries (AI2SQL)
After cleaning, use AI2SQL natural language prompts to generate queries such as:

"Show average billing amount by hospital and gender"

"List top 5 most common medical conditions"

"Show admission trends over time by condition"

"Find patients with abnormal test results by hospital"

Paste screenshots of output for these queries below.

Results
Data was successfully cleaned, deduplicated, and normalized using SQL generated and corrected through AI2SQL.

Key metrics, trends, and patterns now extractable from the cleaned set.

Screenshots attached show transformation steps and final reporting outputs.

Key Fixes & Learning
Corrected AI2SQL prompt syntax for PostgreSQL (e.g., applying DELETE USING for de-duplication).

Improved robustness: null checks, data type conversions, title-case transformations.

Scripted each step for reusability and transparency.

Used native PostgreSQL functions for cleaning and text normalization.
