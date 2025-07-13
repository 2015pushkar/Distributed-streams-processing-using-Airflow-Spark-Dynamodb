# Distributed music streams processing using Airflow, Spark & Dynamodb

![System Architecture](data/images/system_architecture.png)

A **lightweight, serverless‑friendly ETL pipeline** that lands raw music‑stream CSVs in **Amazon S3**, transforms them with **AWS Glue (PySpark)**, and publishes aggregated metrics to **Amazon DynamoDB**—all orchestrated by a concise **Apache Airflow** DAG that runs every five minutes.

---

## ⚙️  How the Pipeline Flows

| Step | Task | What Happens | Proof‑of‑Life |
|------|------|--------------|---------------|
| 1 | `check_files` | Airflow senses new objects in the *incoming* S3 prefix. | – |
| 2 | `glue_pyspark` | Glue Spark job cleans, enriches & repartitions data → writes Parquet back to S3. | ![PySpark Success](data/images/aws_glue_data_transformation_spark_job_success.png) |
| 3 | `glue_dynamo` | Glue Python‑shell job aggregates & upserts records into DynamoDB. | ![Dynamo Load Success](data/images/aws_glue_inserting_into_dynamodb_python_job_success.png) |
| 4 | `move_files` | Original CSVs are archived to a dated folder. | – |

A successful run in the Airflow UI looks like this:

![DAG Success](data/images/airflow_dag_success.png)

Sample intermediate output and DynamoDB view:

<p float="left">
  <img src="data/images/intermediate_spark_output.png" width="48%" />
  <img src="data/images/dynamo_db_final_output.png" width="48%" />
</p>

---

## 🗂️  Repo Layout

```
├─ data/images/                 # PNGs & diagrams
├─ dag-glue-workflow.py         # Airflow DAG (main orchestrator)
├─ glue-pyspark.py              # Spark ETL script
├─ glue-dynamo.py               # DynamoDB loader script
├─ local-docker-development.sh  # One‑shot LocalStack + Airflow dev environment
└─ README.md
```

---

## 🚀  Quick Start (Local)

```bash
# spin up local Airflow + LocalStack
sh local-docker-development.sh
```
Then drop some CSVs into `s3://music-streams/incoming/` (LocalStack bucket) and watch the DAG run.

---

