# pyspark-pipeline-demo
Data Engineering Week 11 Exercise - PySpark Data Processing
Environment: Docker container using quay.io/jupyter/pyspark-notebook:python-3.11
Dataset: NYC Taxi & Limousine Commission (TLC) Yellow Taxi Trip Records, Jan–Mar 2023 + Zone Lookup

1. Project Overview

This project builds a PySpark data processing pipeline demonstrating distributed processing, lazy evaluation, and optimization strategies.
It performs data ingestion, transformation, joins, SQL analysis, and output writing, and documents Spark’s optimizations through .explain() and Spark UI evidence.
An optional MLlib K-Means clustering task is included.

2. Repo Structure
pyspark-pipeline-demo/
│
├── data/                             # Raw input data (Jan–Mar 2023 + zone lookup)
│   ├── yellow_tripdata_2023-01.parquet
│   ├── yellow_tripdata_2023-02.parquet
│   ├── yellow_tripdata_2023-03.parquet
│   └── taxi_zone_lookup.csv
│
├── outputs/                          # Final processed output
│   └── zone_month_summary_parquet/   # Partitioned by month
│
├── src/
│   └── pipeline.html                 # Exported notebook for TA review
│
├── screenshots/                      # Screenshots mapped to rubric
│   ├── Dataset_Selection_and_Load.jpg
│   ├── Transformation_Filtering.jpg
│   ├── SQL1_Execution.jpg
│   ├── SQL2_Execution.jpg
│   ├── Execution_Plan.pdf
│   ├── Output_Writing.jpg
│   ├── Actions_vs_Transformations.jpg
│   ├── SparkUI_Plan.jpg
│   ├── SparkUI_Stages.jpg
│   ├── SparkUI_Environment.jpg
│   ├── SpeedUp.jpg
│   ├── KMeans.jpg
│   └── SparkSession.jpg
│
└── README.md                         # This file

3. Dataset Description

Source: NYC TLC Trip Record Data

Files Used:

yellow_tripdata_2023-01/02/03.parquet

taxi_zone_lookup.csv for zone names and boroughs

Purpose: Enables distributed aggregation of trip volumes, fares, and tips by month and pickup zone.

| **Rubric Component**                        | **Implementation Summary**                                                                                                                                      | **Evidence / Screenshot**                                           |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| **1. Dataset Selection & Loading (10 pts)** | Read 3 Parquet files + zone CSV using `SparkSession.read`. Schema mismatch handled via `unionByName` after casting columns.                                     | `Dataset_Loading.jpg`                                               |
| **2. Transformations (25 pts)**             | Used `withColumn` for derived columns (`pickup_date`, `month`, `tip_pct`), 2+ filters for valid fares and passenger counts.                                     | `Transformation_Filtering.jpg`                                      |
| **3. SQL Queries (20 pts)**                 | Two queries: (1) Top 10 zones by revenue per month using window function; (2) Daily average distance and tips.                                                  | `SQL1_Execution.jpg`, `SQL2_Execution.jpg`                          |
| **4. Optimization (10 pts)**                | Early filtering, column pruning, `broadcast(zones)`, `coalesce(4)` to reduce shuffle. All visible in `.explain()`.                                              | `Execution_Plan.jpg`, notebook cells                                |
| **5. Output Writing (5 pts)**               | Wrote aggregated results as Parquet partitioned by month in `outputs/zone_month_summary_parquet/`.                                                              | `Output_Writing.jpg`                                                |
| **6. Performance Analysis (10 pts)**        | `.explain(mode="formatted")` outputs show Catalyst optimizations (filter pushdown, broadcast join). Spark UI screenshots prove physical plan and stage metrics. | `SparkUI_Plan.jpg`, `SparkUI_Stages.jpg`, `SparkUI_Environment.jpg` |
| **7. Actions vs Transformations (10 pts)**  | Demonstrated lazy transformations (`map`, `filter`) and eager actions (`take`).                                                                                 | `Actions_vs_Transformations.jpg`                                    |
| **8. Caching Bonus (5 pts)**                | Used `.cache()` on `sql2` DataFrame and timed runs to show speedup.                                                                                             | `Cache_Bonus.jpg`                                                   |
| **9. MLlib Bonus (5 pts)**                  | Simple `KMeans(k=3)` on `trip_distance` and `total_amount` features to cluster trips by length × fare.                                                          | `MLLib_Clusters.jpg`, `MLLib_Centers.jpg`                           |


