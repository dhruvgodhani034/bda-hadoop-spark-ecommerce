# Advanced Big Data Processing using HDFS, YARN, MapReduce Abstraction, and Spark/Hive

MSc Data Analytics — Big Data Analytics (IDAN7004)
Dhruv Godhani (Q1117584), Berlin School of Business and Innovation / UCA

A single-node Hadoop cluster built in Google Colab and used to analyse e-commerce
clickstream data, comparing Hive on MapReduce with Spark, and predicting
session-level purchase intent with Spark MLlib.

## Environment

| Component | Version |
|---|---|
| Apache Hadoop | 3.3.6 (pseudo-distributed) |
| Apache Hive | 3.1.3 (MapReduce execution engine) |
| Apache Spark | 3.5.5 (on YARN) |
| Java | Temurin JDK 8 (1.8.0_504) |
| Host | Google Colab VM, 2 vCPU, 12 GiB RAM, 108 GB disk |

Cluster layout: NameNode, Secondary NameNode, 3 DataNodes, ResourceManager and
NodeManager, with a 128 MB block size and replication factor 3.

Java 8 is required because the Hive 3.1.3 CLI fails on Java 11 with a
`ClassCastException` in `SessionState` (Java 9 removed `URLClassLoader`).

## Data

Kaggle: *eCommerce behavior data from multi category store* (REES46), file
`2019-Oct.csv`, published by M. Kechinov (2019).

The data is **not included in this repository**. Kaggle lists its licence as
"copyright-authors", so it is not redistributed here. The notebook downloads it
through the Kaggle API.

An extract was built by keeping complete sessions whose random session ID ends in
`0` or `1`, giving 5,302,032 events (12.5% of the month) in a 707,882,575-byte
file that occupies six 128 MB HDFS blocks.

## Running the notebook

1. Open `BDA_Hadoop_Cluster.ipynb` in Google Colab.
2. Run **Cell R2** first. It mounts Drive, installs Java 8, Hadoop, Hive and Spark,
   formats HDFS only if it has never been formatted, starts all seven daemons and
   loads the extract into HDFS. It is safe to re-run after a Colab reset.
3. Run the remaining sections in order.
4. You will need a Kaggle API token (`kaggle.json`) for the download cell. Never
   commit that file; it is excluded in `.gitignore`.

Colab recycles its virtual machine without warning. If commands hang or report
"Connection refused", re-run Cell R2 before continuing.

## Results

| Measure | Result |
|---|---|
| HDFS blocks / replication | 6 blocks, 3 replicas each, status HEALTHY |
| Ingestion time (`hdfs dfs -put`) | 37.6 s |
| Funnel query, Hive on MapReduce | 181.3 s (3 mappers, 3 reducers) |
| Funnel query, PySpark on YARN | 76.5 s (identical results) |
| Sessions analysed | 1,155,667, of which 6.75% ended in a purchase |
| Logistic regression | AUC 0.802, recall 0.143, F1 0.229 |
| Random forest | AUC 0.840, recall 0.284, F1 0.389 |
| Strongest predictor | Cart activity, 81.3% of feature importance |

## Repository contents

```
BDA_Hadoop_Cluster.ipynb   Colab notebook with outputs preserved
README.md                  This file
.gitignore                 Excludes credentials, data and archives
```

## Note

This repository supports an assessed university assignment. It is shared for
reference only and must not be copied or submitted as another person's work.
