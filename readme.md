# Northwind — Sqoop → Spark → Hive Pipeline

Batch pipeline over the Northwind dataset: Sqoop imports data from PostgreSQL into HDFS
as Parquet, PySpark aggregates it, and results land in a Hive analytics database — all
orchestrated by an Airflow DAG with separate ingest and process task groups.

## Stack
Apache Sqoop · Apache Airflow · PySpark · Apache Hive · HDFS · PostgreSQL

## Pipeline
1. **Ingest** — Sqoop extracts three query results from Postgres into HDFS Parquet
   (top customers by products sold; orders with shipping company; order details).
   Scripts: [extract_table_1.sh](src/ingest/extract_table_1.sh) ·
   [extract_table_2.sh](src/ingest/extract_table_2.sh) ·
   [extract_table_3.sh](src/ingest/extract_table_3.sh)
2. **Transform & load** — PySpark writes into Hive `northwind_analytics`:
   - `products_sold`: companies whose products sold are above the average
     ([processing_table_1.py](src/transform/processing_table_1.py))
   - `products_sent`: discounted orders with computed total price
     ([processing_table_2_3.py](src/transform/processing_table_2_3.py))
3. **Orchestrate** — an Airflow DAG groups the ingest and process stages
   ([sqoop-airflow.py](src/dag/sqoop-airflow.py))

![Airflow DAG](img/dag.png)
![Hive results](img/hive1.png)

---
*Built on the public Northwind sample database.*
