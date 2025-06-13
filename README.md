# AWS-Athena
### What is Amazon Athena?
Amazon Athena is a serverless, interactive query service provided by AWS that allows users to analyze data stored in Amazon S3 using standard SQL queries. It eliminates the need to manage infrastructure, such as provisioning, scaling, or maintaining database instances, as Athena automatically handles all underlying compute resources. Queries are executed in parallel, ensuring fast performance even with large datasets. This makes Athena an ideal choice for users new to data analysis who want a simple, cost-effective way to query data without complex setup.

![image](https://github.com/user-attachments/assets/d7a1c1b9-1e5f-4369-8c3b-6b84438c2280)

- Athena is an AWS service that allows you to **query data stored in Amazon S3 using SQL**.  
- It supports various data types, including log files, order data, and more.  
- **Serverless**, meaning no need to manage infrastructure—AWS handles compute automatically.  
- Executes queries **in parallel** for fast results.  

### **Why Use Athena?**  
Amazon Athena enables users to query structured or semi-structured data stored in S3, such as log files, order data, or any other dataset, using familiar SQL syntax. The results can be further analyzed or visualized using tools like Amazon QuickSight to create reports and dashboards. Common use cases include:
- Querying AWS service logs, such as CloudWatch logs, VPC Flow Logs, or CloudTrail.
- Performing business intelligence tasks, such as analyzing sales or customer data.
- Conducting analytics on any S3 data where SQL queries are applicable.
- **Query logs** (CloudWatch, VPC Flow Logs, CloudTrail).  
- **Business intelligence & analytics** (analyze and visualize data with QuickSight).  
- **Any use case where you need SQL queries on S3 data**.  

### Key Features
- _**Serverless:**_ No need to manage database instances; Athena automatically provisions and scales compute resources.
- _**Parallel Query Execution:**_ Queries are processed in parallel for fast performance.
- _**Integration with AWS Glue Data Catalog:**_ Metadata about S3 data is stored in tables and databases within the AWS Glue Data Catalog.
- _**SQL-Based Querying:**_ Supports standard SQL syntax and functions, making it accessible for users familiar with SQL.
- _**Trino SQL Engine:**_ Athena uses the open-source Trino query engine under the hood, but users do not need to interact with Trino directly.
- _**Pay-Per-Use Pricing:**_ Charges are based on the amount of data scanned during queries, with no upfront costs or infrastructure fees.

### **Pricing:**  
Athena operates on a pay-per-use model, charging $5 per terabyte of data scanned during query execution. For small datasets (e.g., kilobytes or megabytes), costs are typically a few cents per query. As a beginner, you can expect minimal costs (e.g., 1-2 cents for small-scale queries), but always monitor usage and clean up resources to avoid unexpected charges.
- **Pay per query**—$5 per terabyte of data scanned.  
- No upfront costs or infrastructure management.  


Here’s a properly formatted and organized `README.md` for your GitHub repository based on your step-by-step guide:

---

# 📊 Querying S3 Data with Amazon Athena – Beginner Guide

This guide walks you through setting up **Amazon Athena** to query files stored in **Amazon S3** using **SQL**, with help from **AWS Glue**. You'll upload a CSV file which is present in this repo, configure Athena, and run your first queries – all serverless and beginner-friendly.

## 🚀 Steps to Get Started
### 1. Create an S3 Bucket for Your Data

1. Log in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to **S3** by searching "S3" in the top menu.
3. Click **Create bucket**.
4. Provide a unique bucket name (e.g., `files-for-athena-YYYYMMDD`).

   * Replace `YYYYMMDD` with today’s date (e.g., `files-for-athena-20250606`).
   * Bucket names must be globally unique and follow S3 naming rules (lowercase letters, numbers, hyphens).
5. Leave all default settings:

   * **Region:** Choose your preferred AWS region (e.g., `us-east-1`)
   * **Object Ownership:** ACLs disabled (recommended)
   * **Block Public Access:** Enabled (recommended)
   * **Versioning, Tags, Encryption:** Leave as default
6. Click **Create bucket**.
7. Upload your data file:

   * Click the bucket name.
   * Click **Upload**, select a CSV (e.g., order info with `order_id`, `customer_id`, `amount`) – sample file is included in this repo.
   * Click **Upload**.

> 💡 **Note:**
>
> * CSV format is ideal for beginners.
> * Avoid placing the file in subfolders.
> * Supported formats: CSV, JSON, Parquet, ORC, Avro.

![image](https://github.com/user-attachments/assets/710307b2-46b3-423e-bb10-832120d4f5d6)

---

### 2. Configure Athena Query Results Location

Athena stores query results in a **separate, empty S3 bucket**.

#### A. Create Results Bucket

1. Go to **S3** > **Create bucket**.
2. Name it (e.g., `athena-results-YYYYMMDD`).
3. Use the **same region** as your data bucket.
4. Keep default settings and click **Create bucket**.
5. Ensure the bucket is empty.

#### B. Set Query Results Location in Athena

1. Navigate to **Athena** in AWS Console.
2. If first time, click **Set a query result location**.
3. Enter: `s3://athena-results-YYYYMMDD/`
4. Click **Save**.

> 🛡 **Notes:**
>
> * Don’t use the same bucket for both data and results.
> * Make sure IAM has permissions to write to the results bucket.

---

### 3. Create a Table with AWS Glue Crawler

Athena queries **tables**, not raw S3 files. A **crawler** reads your data and creates a table automatically.

#### A. Create a Database

1. Go to **AWS Glue** > **Databases**.
2. Click **Add database**, name it (e.g., `my-first-database`), then **Create**.

#### B. Create a Crawler

1. Go to **Glue** > **Crawlers** > **Create crawler**.
2. Name the crawler (e.g., `my-first-glue-crawler`) > **Next**.
3. **Source type:** Data stores

   * **Data store:** S3
   * Enter path: `s3://files-for-athena-YYYYMMDD/`
   * **Next**
4. **IAM Role:**

   * Choose **Create a new IAM role**, name it (e.g., `AWSGlueServiceRole-Athena`)
   * **Next**
5. **Schedule:** Run on demand > **Next**
6. **Output database:** Select `my-first-database` > **Next**
7. Review and click **Create crawler**
8. Run the crawler.

> 📝 **Check Results:**
> Go to **Tables** in Glue > confirm table (e.g., `files_for_athena`) with correct schema.

![image](https://github.com/user-attachments/assets/17c19038-7d3b-454f-97ef-3ce554acb5bb)

---

### 4. Run Queries in Athena

Now your data is queryable using SQL!

#### Steps:

1. Open **Athena Query Editor**.
2. Select the database (e.g., `my-first-database`).
3. Ensure table (e.g., `files_for_athena`) appears in left panel.

#### Sample Queries:

```sql
-- View sample data
SELECT * FROM files_for_athena LIMIT 10;

-- Top 5 customers by spend
SELECT customer_id, SUM(amount) AS total_spend
FROM files_for_athena
GROUP BY customer_id
ORDER BY total_spend DESC
LIMIT 5;
```

#### View Results:

* Click **Download results** in the editor.
* Or go to `s3://athena-results-YYYYMMDD/` to find CSV output files.

> ⚠️ **Tips:**
>
> * Use `WHERE` clauses to reduce scanned data and cost.
> * Athena supports full SQL (SELECT, JOIN, GROUP BY, etc.).

---

## 🧹 Cleanup Resources

Avoid unwanted charges by deleting everything after you're done:

1. **Delete Athena Table:**

   * Glue > Databases > `my-first-database` > Tables > Delete `files_for_athena`

2. **Delete Glue Database:**

   * Glue > Databases > Delete `my-first-database`

3. **Delete Crawler:**

   * Glue > Crawlers > Delete `my-first-glue-crawler`

4. **Delete S3 Buckets:**

   * Empty each bucket first, then delete:

     * `files-for-athena-YYYYMMDD`
     * `athena-results-YYYYMMDD`

---

## 📘 Additional Notes

* **QuickSight Integration:** Visualize Athena queries in dashboards (advanced).
* **Trino SQL Engine:** Athena uses Trino, but standard SQL is enough for beginners.
* **Permissions Checklist:**

  * `s3:GetObject` for data bucket
  * `s3:PutObject` for results bucket
  * `glue:*` for table/catalog management
  * `athena:*` for running queries
* **CSV File Tips:** Ensure header row exists and format is consistent.
* **Troubleshooting:**

  * Crawler not detecting schema? Check CSV format and permissions.
  * Query errors? Verify table schema and result location config.

---

## Other services you can leverage alongside Athena to create a comprehensive data querying and analysis workflow.
1. S3
2. AWS Glue
3. AWS Redshift
4. Amazon RDS
5. AWS EMR
6. Amazon QuickSight
7. AWS Lambda

<details>
  <summary>Click to View the Breakdown of Key Points in All the Services</summary>

### **1. S3 (Amazon Simple Storage Service)**

Athena works directly with **S3** as the data storage service. All your datasets (e.g., CSV, JSON, Parquet, Avro) are stored in **S3 buckets**. When you query data with Athena, it reads the data directly from these S3 buckets. Athena doesn't store the data itself—**S3 is where the data resides**.

#### Key Points:

* **Storage**: Data is stored in **S3** in the format you prefer.
* **Querying**: Athena uses the **SQL** query engine to read and analyze data directly from S3 files (no data ingestion process is required).
* **Cost**: You pay based on the amount of data scanned by your queries. Using efficient file formats like **Parquet** or **ORC** can reduce costs significantly by minimizing the data scanned.

### **2. AWS Glue (Data Catalog)**

While **S3** is the storage solution, **AWS Glue** helps manage your data schema and metadata, which Athena needs for creating tables and querying data.

* **AWS Glue Data Catalog** acts as the **metadata store** for Athena. It contains the tables, databases, and schema definitions that Athena needs to execute queries.
* **AWS Glue Crawlers** can automatically discover and create metadata for your datasets in S3, making it easier to set up your tables and databases in Athena.

#### Key Points:

* **Metadata Management**: Glue Data Catalog stores metadata about your data in S3.
* **Data Discovery**: The **AWS Glue Crawler** automatically discovers data format and schema from your S3 files and creates tables in the Glue Catalog.
* **Integration**: Athena queries data from S3 using the metadata stored in the Glue Data Catalog. Glue simplifies schema management.

### **3. AWS Redshift (Data Warehouse)**

While **Athena** is a great serverless querying service for **data in S3**, **Amazon Redshift** is a **fully managed data warehouse** where you can perform high-performance SQL queries over large datasets.

You can use **Redshift Spectrum**, a feature of Amazon Redshift, to query data stored in **S3** directly from Redshift without having to move it into the warehouse first. This means you can combine the benefits of S3 storage with the performance of Redshift.

#### Key Points:

* **Data Warehouse**: Redshift is optimized for fast queries over large datasets, with massive parallel processing (MPP).
* **Integration with S3**: Redshift Spectrum allows querying S3 data from within Redshift, enabling you to combine structured data in Redshift with unstructured or semi-structured data in S3.
* **When to Use**: Use Redshift when you need advanced analytics capabilities, data warehousing features, and faster query performance on very large datasets.

### **4. Amazon RDS (Relational Database Service)**

If your data is relational and you prefer to use traditional databases like **MySQL**, **PostgreSQL**, **MariaDB**, or **SQL Server**, **Amazon RDS** provides managed relational database instances.

While **Athena** is a serverless SQL query service for S3 data, you may sometimes want to store and query relational data in a managed database. **RDS** provides a fully managed service to do that.

#### Key Points:

* **Relational Databases**: Use RDS if you need a fully managed relational database for structured data (e.g., OLTP systems).
* **Integration**: You can use **AWS Glue** to load data into RDS, and then query it using SQL.

### **5. AWS EMR (Elastic MapReduce)**

For more complex processing needs, **Amazon EMR** is a managed cluster platform for running **big data frameworks** like **Apache Hadoop**, **Apache Spark**, **Hive**, **Presto**, and **HBase**.

You can use **Presto** (a SQL query engine) on EMR to query large-scale data stored in S3, similar to how you use Athena, but with more control over the environment and scalability.

#### Key Points:

* **Big Data Frameworks**: Use EMR for processing large datasets using frameworks like Spark or Hadoop.
* **Presto**: You can configure Presto on EMR to run SQL queries on S3 data, similar to Athena.
* **When to Use**: Use EMR for more complex, high-performance analytics workflows or when you need custom processing beyond Athena's capabilities.

### **6. Amazon QuickSight (Visualization)**

While **Athena** allows you to query S3 data using SQL, **Amazon QuickSight** is a **business intelligence (BI)** tool that enables you to **visualize** the query results.

You can connect QuickSight to Athena, run queries, and then create interactive dashboards and visualizations. QuickSight supports a variety of chart types, and you can also use it to share insights with stakeholders.

#### Key Points:

* **Data Visualization**: QuickSight helps you create charts, graphs, and dashboards from Athena query results.
* **Integration**: You can directly connect **QuickSight** to **Athena** and visualize S3 data.
* **When to Use**: Use QuickSight for reporting and data visualization in business environments.

### **7. AWS Lambda (Serverless Computing)**

If you need to automate queries, process results, or perform transformations, you can use **AWS Lambda** in combination with Athena.

For example:

* You could trigger a Lambda function based on certain events, like new data arriving in S3.
* The Lambda function could then invoke Athena to run a query, process the result, and store the output in another location.

#### Key Points:

* **Serverless Automation**: Use Lambda to automate workflows and integrate with Athena for data processing tasks.
* **Event-driven**: Lambda can be triggered by events (e.g., file uploads to S3, time schedules, etc.).

</details>

---

## **When to Use Each Service:**

| **Service**    | **Use Case**                                     | **Key Features**                          |
| -------------- | ------------------------------------------------ | ----------------------------------------- |
| **Athena**     | Quick, ad-hoc querying of data in S3             | Serverless, SQL queries on S3 data        |
| **Glue**       | Metadata management and ETL processing           | Data Catalog, Crawlers, Glue Jobs         |
| **Redshift**   | High-performance SQL queries over large datasets | Managed data warehouse, Redshift Spectrum |
| **RDS**        | Relational databases (e.g., MySQL, PostgreSQL)   | Managed SQL databases                     |
| **EMR**        | Big Data processing (Hadoop, Spark)              | Custom clusters for large-scale analytics |
| **QuickSight** | Data visualization and reporting                 | BI dashboards and charts from Athena data |
| **Lambda**     | Serverless automation of queries and processes   | Event-driven, triggered functions         |

---

### **Summary**

* **Amazon S3** is the main storage service for Athena queries.
* **AWS Glue** manages the metadata and schema of your S3 data, helping Athena query it.
* **Redshift Spectrum**, **RDS**, and **EMR** offer more advanced or specialized querying solutions.
* **QuickSight** provides powerful visualization tools for your query results.
* **Lambda** automates workflows around querying and processing data.

Each service has its strengths, so you would choose based on the complexity of your dataset, processing requirements, and scalability needs. Athena is great for ad-hoc querying of data in S3, but if you need more advanced querying, consider Redshift, RDS, or EMR.

---

# Athena uses **PrestoDB** (now called **Trino** after a rebranding), but you can interact with it in various ways, such as through **Trino SQL**, **PySpark**, or **Spark SQL**.
### Which One Should You Use?

* **Trino SQL (Athena)**: If you are looking for serverless, ad-hoc SQL querying over data stored in S3 (or other integrated data sources), Athena (Trino SQL) is your best option. It’s simple, cost-efficient, and scalable without needing to manage any infrastructure.

* **PySpark**: If your task involves more complex, distributed data processing with transformations, machine learning, or big data analysis, then **PySpark** on **EMR** or **AWS Glue** would be the better choice. It allows you to process data in a distributed environment and supports both batch and streaming analytics.

* **Spark SQL**: If you’re running Spark clusters on **EMR** or using **AWS Glue**, and you need to process large datasets with SQL-like queries within a Spark environment, **Spark SQL** is a powerful choice. It’s great for integrating with other Spark components and performing big data analytics.

