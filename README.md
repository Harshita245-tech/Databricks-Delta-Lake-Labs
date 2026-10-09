
# Databricks Delta Lake Labs

## Hands-on Delta Lake Implementation using Databricks

---

## 1. Project Overview

This repository contains the hands-on implementation of **Delta Lake concepts using Databricks** as part of Data Engineering training.

The project was implemented using **Databricks Free Edition** and demonstrates the practical use of Delta Lake for creating, modifying, maintaining, versioning, and tracking changes in data.

A sample customer dataset was used throughout the exercises. The main Delta table used in the project is:

```text
lab_db.delta_customers
```

The exercises cover multiple important Delta Lake features, including:

- Delta table creation
- Data insertion
- Schema evolution
- Schema inspection
- Transaction history
- Delta Lake versioning
- Time Travel
- Shallow Clone
- Deep Clone
- Clone verification
- OPTIMIZE
- VACUUM
- Change Data Feed
- UPDATE operations
- MERGE operations
- Updating existing records
- Inserting new records
- Final data verification

The project provides practical experience with the features commonly used while building reliable and maintainable Data Engineering pipelines.

---

# 2. Objectives

The main objectives of this project are:

1. To understand the fundamentals of Delta Lake.
2. To create and manage Delta tables using Databricks.
3. To understand how Delta Lake stores transaction history.
4. To demonstrate schema evolution.
5. To understand Delta Lake table versions.
6. To retrieve previous versions of data using Time Travel.
7. To create shallow and deep clones of Delta tables.
8. To understand the difference between shallow and deep cloning.
9. To perform Delta table maintenance.
10. To optimize Delta tables using OPTIMIZE.
11. To understand and perform VACUUM operations.
12. To enable Change Data Feed.
13. To read row-level changes using Change Data Feed.
14. To perform UPDATE operations.
15. To perform MERGE operations.
16. To demonstrate both UPDATE and INSERT behavior through MERGE.
17. To verify the final state of the customer table.
18. To gain practical experience with Delta Lake using Databricks SQL and PySpark.

---

# 3. Technologies Used

| Technology | Purpose |
|---|---|
| Databricks | Cloud-based Data Engineering platform |
| Delta Lake | Reliable storage and transaction layer |
| Apache Spark | Distributed data processing |
| PySpark | Python API for Spark |
| Spark SQL | SQL-based processing |
| SQL | Delta table management and data operations |
| Databricks Serverless | Compute environment |
| GitHub | Source code and project documentation |

---

# 4. Project Structure

The repository contains the project README, SQL queries, screenshots of completed executions, and supporting documentation.

```text
Databricks-Delta-Lake-Labs/
│
├── README.md
├── Delta_Lake_Labs.sql
├── screenshots/
└── documentation/
```

The `screenshots` folder contains the screenshots captured during the execution and verification of the labs.

---

# 5. Dataset Description

A sample customer dataset was used for the Delta Lake exercises.

The main table used throughout the project is:

```text
lab_db.delta_customers
```

The customer table contains the following fields:

| Column | Data Type | Description |
|---|---|---|
| `customer_id` | INT | Unique customer identifier |
| `name` | STRING | Customer name |
| `email` | STRING | Customer email address |
| `signup_date` | DATE | Customer signup date |
| `status` | STRING | Current customer status |
| `membership_tier` | STRING | Customer membership level |

Example records used during the exercises include:

```text
101 | Alice Smith | alice@example.com | 2024-01-15 | Active
103 | Charlie Brown | charlie@example.com | 2024-03-10 | Active | Tier-1
```

Later exercises introduced additional customer data and modifications.

---

# 6. LAB 1 – Delta Table Creation

## Objective

The first lab focused on creating and working with a Delta table.

Delta Lake tables provide transactional capabilities on top of data stored in a lakehouse environment.

The customer data was stored in a Delta table named:

```text
lab_db.delta_customers
```

A database/schema named `lab_db` was used for the exercises.

The basic table creation and data loading process was performed using Databricks SQL and PySpark.

---

## PySpark DataFrame Creation

A new customer record was created using PySpark:

```python
from pyspark.sql.types import *

df_new = spark.createDataFrame([
    (103, "Charlie Brown", "charlie@example.com", "2024-03-10", "Active", "Tier-1")
], ["customer_id", "name", "email", "signup_date", "status", "membership_tier"])
```

The resulting DataFrame contained the following schema:

```text
customer_id      long
name             string
email            string
signup_date      string
status           string
membership_tier  string
```

---

## Writing Data as Delta

The DataFrame was written into the Delta table using:

```python
df_new.write.format("delta") \
    .mode("append") \
    .option("mergeSchema", "true") \
    .saveAsTable("lab_db.delta_customers")
```

The important options used were:

- `format("delta")` – writes the data in Delta format.
- `mode("append")` – appends the new record to the existing table.
- `mergeSchema` – allows the schema to evolve when new columns are introduced.
- `saveAsTable()` – stores the result as a managed table.

---

# 7. Schema Evolution

Schema evolution was demonstrated by introducing the `membership_tier` column.

The schema was inspected after the operation.

The resulting schema contained:

```text
customer_id       int
name              string
email             string
signup_date       date
status            string
membership_tier   string
```

The schema inspection confirmed that the new `membership_tier` field was successfully added.

This demonstrates how Delta Lake can evolve an existing table schema without recreating the entire table.

---

# 8. Data Verification

The customer data was queried to verify the contents of the Delta table.

Example SQL:

```sql
SELECT *
FROM lab_db.delta_customers;
```

The resulting data included customer records such as:

```text
101 | Alice Smith   | alice@example.com   | 2024-01-15 | Active   | NULL
103 | Charlie Brown | charlie@example.com | 2024-03-10 | Active   | Tier-1
```

The output confirmed that the data was successfully stored in the Delta table.

---

# 9. LAB 2 – Delta Table Data Operations

## Objective

The second lab focused on performing data operations on the Delta table and understanding how Delta Lake records those operations.

An example insertion was performed using:

```sql
INSERT INTO delta_customers VALUES
(101, 'Alice Smith', 'alice@example.com', '2024-01-15', 'Active');
```

The table was then used for further Delta Lake exercises.

Delta Lake automatically maintains transaction information for operations performed on the table.

---

# 10. LAB 3 – Delta Transaction History

## Objective

The third lab focused on inspecting the transaction history of the Delta table.

Delta Lake maintains a transaction log that records changes made to the table.

The transaction history was inspected using:

```sql
DESCRIBE HISTORY delta_customers;
```

The table history displayed information such as:

- Version
- Timestamp
- User ID
- User Name
- Operation
- Operation Parameters

The history included operations such as:

```text
WRITE
DELETE
```

The screenshots show multiple Delta table versions created by the different operations.

For example, the transaction history displayed version numbers including:

```text
Version 5
Version 6
Version 7
Version 8
Version 9
```

This demonstrates that Delta Lake maintains a versioned history of changes.

---

# 11. LAB 4 – Delta Time Travel

## Objective

The fourth lab focused on accessing historical versions of the Delta table using Delta Lake Time Travel.

Time Travel allows users to query an earlier version of a Delta table.

The transaction history was first inspected using:

```sql
DESCRIBE HISTORY lab_db.delta_customers;
```

---

## Time Travel Using Version Number

The table was queried at version 5 using:

```sql
SELECT *
FROM lab_db.delta_customers VERSION AS OF 5
ORDER BY customer_id;
```

The result returned the customer records that existed at that historical version.

The output showed:

```text
customer_id | name          | email               | signup_date | status | membership_tier
101         | Alice Smith   | alice@example.com   | 2024-01-15  | Active | NULL
103         | Charlie Brown | charlie@example.com | 2024-03-10  | Active | Tier-1
```

This demonstrates that Delta Lake can retrieve a previous state of a table without manually maintaining separate backup tables.

---

# 12. Delta Shallow Clone

A shallow clone was created using:

```sql
CREATE OR REPLACE TABLE lab_db.delta_customers_shallow
SHALLOW CLONE lab_db.delta_customers;
```

The shallow clone creates a new Delta table while referencing the existing data files rather than independently copying all underlying data files.

The operation was successfully executed.

---

# 13. Delta Deep Clone

A deep clone was created using:

```sql
CREATE OR REPLACE TABLE lab_db.delta_customers_deep
DEEP CLONE lab_db.delta_customers;
```

A deep clone creates an independent copy of the table's data and metadata.

The deep clone operation was successfully executed.

---

# 14. Clone Verification

The original table and the two cloned tables were compared using row counts.

The verification query used was:

```sql
SELECT 'Original' AS table_name, COUNT(*) AS row_count
FROM lab_db.delta_customers

UNION ALL

SELECT 'Shallow Clone', COUNT(*)
FROM lab_db.delta_customers_shallow

UNION ALL

SELECT 'Deep Clone', COUNT(*)
FROM lab_db.delta_customers_deep;
```

The result was:

| Table | Row Count |
|---|---:|
| Original | 2 |
| Shallow Clone | 2 |
| Deep Clone | 2 |

This confirmed that the original table and both clones contained the expected number of records.

---

# 15. LAB 5 – Delta Table Maintenance

## Objective

The fifth lab focused on maintaining Delta tables using:

- OPTIMIZE
- VACUUM DRY RUN
- VACUUM

These operations help manage Delta table storage and performance.

---

# 16. OPTIMIZE

The Delta table was optimized using:

```sql
OPTIMIZE lab_db.delta_customers;
```

The OPTIMIZE command returned metrics related to file management.

The result included information such as:

- Number of files added
- Number of files removed
- File sizes
- Total files
- Total size

The execution successfully completed.

OPTIMIZE is used to improve query performance by compacting small files into larger files.

---

# 17. VACUUM DRY RUN

Before performing the actual cleanup, a VACUUM dry run was performed.

The query used was:

```sql
VACUUM lab_db.delta_customers
RETAIN 168 HOURS
DRY RUN;
```

The retention period used was:

```text
168 HOURS
```

which corresponds to:

```text
7 DAYS
```

The `DRY RUN` option was used to preview the files that could potentially be removed without actually deleting them.

The execution completed successfully.

---

# 18. VACUUM Cleanup

After the dry run, the actual VACUUM operation was executed:

```sql
VACUUM lab_db.delta_customers
RETAIN 168 HOURS;
```

The operation successfully completed.

The retention period was again set to 168 hours.

VACUUM is used to clean up old data files that are no longer required according to the configured retention period.

---

# 19. LAB 6 – Change Data Feed

## Objective

The sixth lab focused on Delta Lake **Change Data Feed (CDF)**.

Change Data Feed allows changes made to rows in a Delta table to be tracked and queried.

The feature was enabled using:

```sql
ALTER TABLE lab_db.delta_customers
SET TBLPROPERTIES (delta.enableChangeDataFeed = true);
```

The command returned:

```text
OK
```

This confirmed that Change Data Feed was successfully enabled.

---

# 20. Creating a Change

After enabling Change Data Feed, a customer record was updated.

The following query was executed:

```sql
UPDATE lab_db.delta_customers
SET status = 'Inactive'
WHERE customer_id = 101;
```

The execution result showed:

```text
num_affected_rows = 1
```

This confirmed that one customer record was modified.

Customer 101 changed from:

```text
Active
```

to:

```text
Inactive
```

---

# 21. Reading Change Data Feed

The changes were then queried using:

```sql
SELECT *
FROM table_changes('lab_db.delta_customers', 14);
```

The output displayed two rows corresponding to the update:

```text
update_preimage
update_postimage
```

The `update_preimage` represented the record before the update.

The `update_postimage` represented the record after the update.

The result showed customer 101 before and after the status change.

Example:

```text
customer_id = 101
name = Alice Smith
email = alice@example.com
signup_date = 2024-01-15
```

Before:

```text
status = Active
```

After:

```text
status = Inactive
```

The output also contained Delta Change Data Feed metadata such as:

```text
_change_type
_commit_version
_commit_timestamp
```

This successfully demonstrated row-level change tracking.

---

# 22. Final Customer Data After CDF Operation

The final customer data was checked using:

```sql
SELECT *
FROM lab_db.delta_customers
ORDER BY customer_id;
```

The output showed:

```text
101 | Alice Smith   | alice@example.com   | 2024-01-15 | Inactive | NULL
103 | Charlie Brown | charlie@example.com | 2024-03-10 | Active   | Tier-1
```

This confirmed that the update was successfully applied to the Delta table.

---

# 23. LAB 7 – MERGE Operation

## Objective

The seventh lab focused on using the Delta Lake `MERGE` command.

MERGE is commonly used for implementing **upsert** logic.

An upsert means:

- Update an existing record when a matching record is found.
- Insert a new record when no matching record exists.

---

# 24. MERGE – Updating an Existing Customer

Customer 103 was used to demonstrate the update functionality.

The source data contained updated information for customer 103.

The MERGE structure used was:

```sql
MERGE INTO lab_db.delta_customers AS target
USING (
    SELECT
        103 AS customer_id,
        'Charlie Brown Updated' AS name,
        'charlie_new@example.com' AS email,
        '2024-03-10' AS signup_date,
        'Active' AS status,
        'Tier-2' AS membership_tier
) AS source
ON target.customer_id = source.customer_id

WHEN MATCHED THEN
    UPDATE SET
        target.name = source.name,
        target.email = source.email,
        target.status = source.status,
        target.membership_tier = source.membership_tier

WHEN NOT MATCHED THEN
    INSERT *;
```

The MERGE operation successfully updated the existing customer.

The result showed:

```text
num_affected_rows = 1
num_updated_rows = 1
num_deleted_rows = 0
num_inserted_rows = 0
```

This confirmed that an existing record was updated rather than inserted.

---

# 25. Verifying the MERGE Update

The updated customer was verified using:

```sql
SELECT *
FROM lab_db.delta_customers
WHERE customer_id = 103;
```

The output showed the updated information for customer 103.

The customer record contained:

```text
Customer ID: 103
Name: Charlie Brown Updated
Email: charlie_new@example.com
Signup Date: 2024-03-10
Status: Active
Membership Tier: Tier-2
```

This confirmed that the MERGE update was successful.

---

# 26. MERGE – Inserting a New Customer

A second MERGE operation was performed to demonstrate insertion of a new customer.

Customer 104 was used as the new record.

The source data was:

```text
104 | David Kumar | david@example.com | 2024-04-01 | Active | Tier-1
```

The MERGE operation used the following structure:

```sql
MERGE INTO lab_db.delta_customers AS target
USING (
    SELECT
        104 AS customer_id,
        'David Kumar' AS name,
        'david@example.com' AS email,
        '2024-04-01' AS signup_date,
        'Active' AS status,
        'Tier-1' AS membership_tier
) AS source
ON target.customer_id = source.customer_id

WHEN MATCHED THEN
    UPDATE SET
        target.name = source.name,
        target.email = source.email,
        target.status = source.status,
        target.membership_tier = source.membership_tier

WHEN NOT MATCHED THEN
    INSERT *;
```

Since customer 104 did not already exist in the target table, the `WHEN NOT MATCHED` condition was triggered.

The result showed:

```text
num_affected_rows = 1
num_updated_rows = 0
num_deleted_rows = 0
num_inserted_rows = 1
```

This confirmed that a new customer was successfully inserted.

---

# 27. Final Data Verification

The final table contents were verified after completing the Delta Lake operations.

The query used was:

```sql
SELECT *
FROM lab_db.delta_customers
ORDER BY customer_id;
```

The final dataset contained the modified customer information and the newly inserted customer.

The main records demonstrated through the labs were:

```text
101 | Alice Smith | alice@example.com | 2024-01-15 | Inactive | NULL
103 | Charlie Brown Updated | charlie_new@example.com | 2024-03-10 | Active | Tier-2
104 | David Kumar | david@example.com | 2024-04-01 | Active | Tier-1
```

The final verification confirmed that the UPDATE and MERGE operations were successfully applied.

---

# 28. Complete SQL Commands Used

The following are the major SQL commands used during the Delta Lake exercises.

## Transaction History

```sql
DESCRIBE HISTORY delta_customers;
```

```sql
DESCRIBE HISTORY lab_db.delta_customers;
```

---

## Time Travel

```sql
SELECT *
FROM lab_db.delta_customers VERSION AS OF 5
ORDER BY customer_id;
```

---

## Shallow Clone

```sql
CREATE OR REPLACE TABLE lab_db.delta_customers_shallow
SHALLOW CLONE lab_db.delta_customers;
```

---

## Deep Clone

```sql
CREATE OR REPLACE TABLE lab_db.delta_customers_deep
DEEP CLONE lab_db.delta_customers;
```

---

## Clone Verification

```sql
SELECT 'Original' AS table_name, COUNT(*) AS row_count
FROM lab_db.delta_customers

UNION ALL

SELECT 'Shallow Clone', COUNT(*)
FROM lab_db.delta_customers_shallow

UNION ALL

SELECT 'Deep Clone', COUNT(*)
FROM lab_db.delta_customers_deep;
```

---

## OPTIMIZE

```sql
OPTIMIZE lab_db.delta_customers;
```

---

## VACUUM Dry Run

```sql
VACUUM lab_db.delta_customers
RETAIN 168 HOURS
DRY RUN;
```

---

## VACUUM Cleanup

```sql
VACUUM lab_db.delta_customers
RETAIN 168 HOURS;
```

---

## Enable Change Data Feed

```sql
ALTER TABLE lab_db.delta_customers
SET TBLPROPERTIES (delta.enableChangeDataFeed = true);
```

---

## Update Customer

```sql
UPDATE lab_db.delta_customers
SET status = 'Inactive'
WHERE customer_id = 101;
```

---

## Read Change Data Feed

```sql
SELECT *
FROM table_changes('lab_db.delta_customers', 14);
```

---

## Verify Final Data

```sql
SELECT *
FROM lab_db.delta_customers
ORDER BY customer_id;
```

---

## MERGE – Update Existing Record

```sql
MERGE INTO lab_db.delta_customers AS target
USING (
    SELECT
        103 AS customer_id,
        'Charlie Brown Updated' AS name,
        'charlie_new@example.com' AS email,
        '2024-03-10' AS signup_date,
        'Active' AS status,
        'Tier-2' AS membership_tier
) AS source
ON target.customer_id = source.customer_id

WHEN MATCHED THEN
    UPDATE SET
        target.name = source.name,
        target.email = source.email,
        target.status = source.status,
        target.membership_tier = source.membership_tier

WHEN NOT MATCHED THEN
    INSERT *;
```

---

## MERGE – Insert New Record

```sql
MERGE INTO lab_db.delta_customers AS target
USING (
    SELECT
        104 AS customer_id,
        'David Kumar' AS name,
        'david@example.com' AS email,
        '2024-04-01' AS signup_date,
        'Active' AS status,
        'Tier-1' AS membership_tier
) AS source
ON target.customer_id = source.customer_id

WHEN MATCHED THEN
    UPDATE SET
        target.name = source.name,
        target.email = source.email,
        target.status = source.status,
        target.membership_tier = source.membership_tier

WHEN NOT MATCHED THEN
    INSERT *;
```

---

# 29. PySpark Code Used

A PySpark DataFrame was also used to demonstrate writing data into the Delta table.

```python
from pyspark.sql.types import *

df_new = spark.createDataFrame([
    (103, "Charlie Brown", "charlie@example.com", "2024-03-10", "Active", "Tier-1")
], ["customer_id", "name", "email", "signup_date", "status", "membership_tier"])

df_new.write.format("delta") \
    .mode("append") \
    .option("mergeSchema", "true") \
    .saveAsTable("lab_db.delta_customers")
```

This demonstrated how PySpark can be used together with Delta Lake.

---

# 30. Important Concepts Learned

## Delta Lake

Delta Lake is a storage layer that provides reliability and transactional capabilities for data stored in a data lake environment.

---

## ACID Transactions

Delta Lake supports reliable data modifications through transactional processing.

This helps maintain consistency when multiple operations are performed on a table.

---

## Transaction Log

Delta Lake maintains transaction information that records operations performed against a table.

The transaction history can be inspected using:

```sql
DESCRIBE HISTORY lab_db.delta_customers;
```

---

## Schema Evolution

Schema evolution allows the structure of a Delta table to change as new fields are introduced.

In the project, the `membership_tier` column was introduced using:

```python
.option("mergeSchema", "true")
```

---

## Time Travel

Time Travel allows users to query an earlier version of a Delta table.

Example:

```sql
SELECT *
FROM lab_db.delta_customers VERSION AS OF 5;
```

---

## Shallow Clone

A shallow clone creates a new table while referencing the existing underlying data.

Example:

```sql
SHALLOW CLONE lab_db.delta_customers;
```

---

## Deep Clone

A deep clone creates an independent copy of the table data and metadata.

Example:

```sql
DEEP CLONE lab_db.delta_customers;
```

---

## OPTIMIZE

OPTIMIZE is used for Delta table maintenance and file compaction.

```sql
OPTIMIZE lab_db.delta_customers;
```

---

## VACUUM

VACUUM removes old data files that are no longer required according to the retention policy.

The project used a retention period of:

```text
168 hours = 7 days
```

---

## Change Data Feed

Change Data Feed provides row-level information about changes made to a Delta table.

The project demonstrated:

```text
update_preimage
update_postimage
```

---

## MERGE

MERGE provides an efficient way to implement upsert logic.

The project demonstrated both:

```text
WHEN MATCHED
```

for updating existing records and:

```text
WHEN NOT MATCHED
```

for inserting new records.

---

# 31. Lab-wise Summary

| Lab | Topic | Status |
|---|---|---|
| Lab 1 | Delta Table Creation | Completed |
| Lab 2 | Data Operations / Schema Evolution | Completed |
| Lab 3 | Delta Transaction History | Completed |
| Lab 4 | Time Travel | Completed |
| Lab 4 | Shallow Clone | Completed |
| Lab 4 | Deep Clone | Completed |
| Lab 4 | Clone Verification | Completed |
| Lab 5 | OPTIMIZE | Completed |
| Lab 5 | VACUUM Dry Run | Completed |
| Lab 5 | VACUUM Cleanup | Completed |
| Lab 6 | Change Data Feed | Completed |
| Lab 6 | Update and CDF Verification | Completed |
| Lab 7 | MERGE Update | Completed |
| Lab 7 | MERGE Insert | Completed |
| Lab 7 | Final Verification | Completed |

---

# 32. Verification Results

The following results were successfully observed during the exercises:

### Clone Verification

```text
Original       → 2 rows
Shallow Clone → 2 rows
Deep Clone    → 2 rows
```

### VACUUM

The VACUUM dry run and cleanup commands executed successfully with a retention period of 168 hours.

### Change Data Feed

The CDF output successfully showed:

```text
update_preimage
update_postimage
```

for customer 101.

### MERGE Update

The MERGE operation for customer 103 returned:

```text
num_affected_rows = 1
num_updated_rows = 1
num_inserted_rows = 0
```

### MERGE Insert

The MERGE operation for customer 104 returned:

```text
num_affected_rows = 1
num_updated_rows = 0
num_inserted_rows = 1
```

These results confirm that both MERGE update and insert scenarios were successfully implemented.

---

# 33. Skills Demonstrated

Through this project, the following Data Engineering skills were demonstrated:

- Databricks
- Delta Lake
- Apache Spark
- PySpark
- Spark SQL
- SQL
- Delta table management
- Schema evolution
- Transaction management
- Data versioning
- Time Travel
- Table cloning
- Data maintenance
- File optimization
- VACUUM
- Change Data Feed
- Data updates
- MERGE / Upsert operations
- Data validation
- Data verification

---

# 34. Practical Applications

The concepts implemented in these labs are useful in real-world Data Engineering scenarios.

### Historical Data Analysis

Time Travel can be used to access previous versions of datasets for auditing and analysis.

### Data Recovery

Historical versions can help recover data after accidental modifications.

### Data Auditing

Transaction history provides information about operations performed on Delta tables.

### Incremental Data Processing

Change Data Feed can be used to identify changes and process only modified records.

### Data Synchronization

MERGE operations are useful when synchronizing source systems with target tables.

### Data Maintenance

OPTIMIZE and VACUUM help manage Delta table storage and performance.

### Testing and Development

Shallow and deep clones can be used to create copies of tables for testing and development scenarios.

---

# 35. Key Learning Outcomes

After completing these exercises, the following concepts were practically implemented and understood:

1. How to create and manage Delta tables.
2. How Delta Lake maintains table versions.
3. How schema evolution works.
4. How transaction history can be inspected.
5. How Time Travel can retrieve previous table versions.
6. How shallow and deep clones can be created.
7. How to verify cloned tables.
8. How OPTIMIZE can be used for table maintenance.
9. How VACUUM can clean up obsolete files.
10. How Change Data Feed can capture row-level changes.
11. How updates appear as pre-image and post-image records.
12. How MERGE performs upsert operations.
13. How MERGE updates existing records.
14. How MERGE inserts new records.
15. How to verify the final state of a Delta table.
16. How SQL and PySpark can be used together in Databricks.

---

# 36. Final Project Checklist

The following tasks were completed as part of the project:

- [x] Created Delta customer table
- [x] Loaded customer data
- [x] Demonstrated schema evolution
- [x] Added membership tier information
- [x] Inspected table schema
- [x] Inspected Delta transaction history
- [x] Performed Time Travel
- [x] Created shallow clone
- [x] Created deep clone
- [x] Verified original and cloned tables
- [x] Performed OPTIMIZE
- [x] Performed VACUUM dry run
- [x] Performed VACUUM cleanup
- [x] Enabled Change Data Feed
- [x] Created an UPDATE operation
- [x] Read Change Data Feed
- [x] Verified pre-image and post-image
- [x] Performed MERGE update
- [x] Verified MERGE update
- [x] Performed MERGE insert
- [x] Verified MERGE insert
- [x] Performed final data verification
- [x] Captured screenshots of the completed exercises
- [x] Prepared project documentation

---

# 37. Conclusion

This project provided practical hands-on experience with **Delta Lake using Databricks**.

The exercises covered the complete lifecycle of working with a Delta table, starting from creating and loading customer data and continuing through schema evolution, transaction history, historical data access, cloning, maintenance, change tracking, and MERGE-based upsert operations.

The project demonstrated how Delta Lake provides important capabilities for modern Data Engineering workflows, including reliable data management, historical versioning, auditing, incremental change processing, and efficient data updates.

The successful execution and verification of all the labs provided practical understanding of how Delta Lake features can be applied when building reliable and maintainable lakehouse data pipelines.

---

# 38. Repository Information

**Repository Name:**

```text
Databricks-Delta-Lake-Labs
```

**Platform:**

```text
Databricks Free Edition
```

**Primary Technologies:**

```text
Databricks
Delta Lake
Apache Spark
PySpark
Spark SQL
SQL
```

**Project Type:**

```text
Data Engineering / Delta Lake Hands-on Labs
```

**Status:**

```text
Completed
```

---



Data Engineering Training Project
```
