# AWS-Athena
### What is Amazon Athena?
Amazon Athena is a serverless, interactive query service provided by AWS that allows users to analyze data stored in Amazon S3 using standard SQL queries. It eliminates the need to manage infrastructure, such as provisioning, scaling, or maintaining database instances, as Athena automatically handles all underlying compute resources. Queries are executed in parallel, ensuring fast performance even with large datasets. This makes Athena an ideal choice for users new to data analysis who want a simple, cost-effective way to query data without complex setup.
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

## ✅ Conclusion

Amazon Athena offers a simple way to query structured data in S3 using SQL—without managing servers. By combining S3, Glue, and Athena:

* You can run powerful SQL queries.
* You avoid infrastructure complexity.
* You keep costs under control with proper cleanup and query efficiency.

As you grow, explore features like partitioning, compression, and visual reporting with QuickSight.

---



