# Microsoft Fabric Lakehouse: Implementing Medallion Architecture
This project implements the Medallion Architecture model within a Lakehouse (Data Lakehouse) environment on the Microsoft Fabric platform. Throughout the project, data processing stages are divided into layers: "bronze," "silver," and "gold." The goal is to transform raw data into meaningful and analyzable insights.

## 🔍 Objective
Create a Fabric workspace

Set up the Lakehouse structure

Load raw data into the Bronze layer

Clean and transform data into Delta format in the Silver layer

Move processed data to the Gold layer

Establish relationships with a Power BI semantic model

## ✅ 1. Creating a Workspace
The first step to working with Microsoft Fabric is creating a workspace.

Logged in at https://app.fabric.microsoft.com

Created a new workspace via the "Workspaces" tab

Selected Fabric Trial for licensing

Activated Data Model Editing Preview feature (required for Power BI semantic model)

## 🏛️ 2. Lakehouse and Bronze Layer Data
After creating a new Lakehouse (named "Sales"), we began loading data.

Downloaded and extracted the orders.zip file

Obtained files: 2019.csv, 2020.csv, 2021.csv

Lakehouse > Files > New folder > Added bronze folder

Uploaded these 3 CSV files to the bronze folder

This phase represents the layer where raw data is stored. Data remains unprocessed here.

## ⚙️ 3. Transformation to Silver Layer
Created a notebook and processed data from the bronze layer using PySpark.

### Processed Steps:
Defined data schema using StructType

Loaded all .csv files into a DataFrame

Viewed data structure with display(df.head(10))

### Data Cleaning:
Added source filename with input_file_name()

Flagged pre-2019-08-01 data with IsFlagged column

Added timestamps with CreatedTS and ModifiedTS

Set empty CustomerName to "Unknown"

Delta Table Definition:
Created sales.sales_silver table with DeltaTable.createIfNotExists

Added transformed columns to the schema

In this phase, data is clean, enriched, and ready for analysis.

## 📊 4. Gold Layer and Semantic Model
### 📂 What Did I Do?
Using cleaned and related data from the Silver layer, I created an aggregated Gold table.

In this table, I summarized sales data by year, country, and product category (using expressions like SUM(sales_amount)).

Saved the Gold table in Delta format.

### 📊 Semantic Model Connection
Connected the Gold table to a Power BI Semantic Model.

In Power BI, defined this table as a fact table.

Added country and category information as separate dimension tables to create a dimension/fact structure.

This structure enables faster and more meaningful analysis in Power BI reports.

### 🧠 Why Is This Step Important?
The Gold layer is where data is processed and ready for end users.

Semantic model gives meaning to data: Ensures reporting consistency (e.g., "Annual Sales," "Total Revenue" are the same for everyone).

Also important for performance: Power BI processes pre-modeled data faster.

### 💡 Extra Info
Semantic modeling in Power BI is the foundation of enterprise BI solutions in data warehouse design.

This step lets users quickly filter reports, drill down, and gain meaningful insights.

## ✍️ Why This Architecture?
Bronze: Raw data, minimally processed. Goal is secure data storage.

#### Silver: Clean, schematized, analyzable data.

#### Gold: Optimized, summarized data for business intelligence reporting.

#### Delta format: ACID-compliant, version-controlled, high-performance data storage.

📅 Estimated Time: 45 Minutes
### 🚀 Technologies Used
Microsoft Fabric

Lakehouse (Data Lakehouse)

PySpark & Spark SQL

Delta Lake

Power BI Semantic Model
```
 📁 Folder Structure
├── bronze
│   ├── 2019.csv
│   ├── 2020.csv
│   └── 2021.csv
├── silver
│   └── sales_silver (Delta table)
├── gold
    └── (Gold table to be defined)
```

This project serves as a guide for data professionals who want to truly understand and implement the Medallion Architecture model in a Microsoft Fabric environment.✨

![1](./images/1.png)

![2](./images/2.png)

![3](./images/3.png)

![4,0](./images/4,0.png)
![4,1](./images/4,1.png)
![4,2](./images/4,2.png)

![5](./images/5.png)

![6](./images/6.png)

![7](./images/7.png)

![8](./images/8.png)

![9](./images/9.png)

![10](./images/10.png)

![11,0](./images/11,0.png)
![11,1](./images/11,1.png)
![11,2](./images/11,2.png)
![11,3](./images/11,3.png)
![11,4](./images/11,4.png)

![12,0](./images/12,0.png)
![12,1](./images/12,1.png)
![12,2](./images/12,2.png)
![12,3](./images/12,3.png)

![13,0](./images/13,0.png)
![13,1](./images/13,1.png)
![13,2](./images/13,2.png)
![13,3](./images/13,3.png)

![14](./images/14.png)

![15](./images/15.png)
