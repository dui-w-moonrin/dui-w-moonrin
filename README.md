
# Dui – Watcharapong Moonrin
Target Role: Data Engineer 
SQL-first career switcher with an IT and software development background, pivoting into a data career 

[![GitHub Streak](https://streak-stats.demolab.com?user=dui-w-moonrin&theme=radical)](https://git.io/streak-stats)

[![Ashutosh's github activity graph](https://github-readme-activity-graph.vercel.app/graph?username=dui-w-moonrin&theme=dracula)](https://github.com/ashutosh00710/github-readme-activity-graph)

## 🧰 My Repo Code Stack

<p align="center">
  <img
    src="https://github-top-languages-nu.vercel.app/api/languages?count=10&theme=dark&title=Dui%27s%20Code%20Stack&width=500&height=320&stroke=true"
    alt="Dui's Top Programming Languages in this repository"
  />
</p>

## What I'm focusing on
- Data Engineering: PySpark, Apache Airflow, ETL Pipeline Design, Data Modeling (Medallion Architecture) 
- Cloud & Big Data: Google Cloud Platform (GCS, BigQuery, Cloud Composer, Dataproc Serverless)  
- Data Processing: Python, PySpark DataFrame API, Databricks Environment, Parquet, Data Transformation, Data Validation  
- Orchestration & Runtime: Apache Airflow (DAG design), DataprocCreateBatchOperator, Kubernetes (Composer-managed 
environment) 
- Data Quality: Data validation, quality checks, quarantine handling, cross-table relationship validation 
- Databases & Querying: PostgreSQL, MySQL, SQL Server (CTEs, joins, window functions) 
- Runtime & Environment: Docker Compose, Cloud Shell, Linux CLI, environment-driven configuration 
- Tools: Git, Docker, VS Code, DBeaver 

## Featured Projects
- [Amazon Books Data Engineering Pipeline (End-to-End) — Databricks Edition](https://github.com/dui-w-moonrin/amazon-books-de-databricks/) 

## 🎯 Business Case
![Business Use Case](https://github.com/dui-w-moonrin/amazon-books-de-databricks/raw/main/images/business_use_case.png)

## Archetecture Diagram
![Architecture Diagram](https://github.com/dui-w-moonrin/amazon-books-de-databricks/raw/main/images/delta_lake.png)

### 🧱 Entity Relationship - ERD

```mermaid
erDiagram
    Direction LR
    BOOKS_DATA ||--o{ BOOKS_RATING : joins_on_title_candidate

    BOOKS_DATA {
        string title_hash PK
        string description
        string authors
        string image_url
        string preview_link
        string publisher
        string published_date_raw
        string info_link
        string categories
        string ratings_count_raw
    }

    BOOKS_RATING {
        string title_hash FK
        string price_raw
        string user_id
        string profile_name
        string reviewhelpfulness
        string review_score
        string review_time_raw
        string review_summary
        string review_text
    }
```
## DAGS
![DAGS](https://github.com/dui-w-moonrin/amazon-books-de-databricks/raw/main/images/dags.png)

## Timeline
![DAGS](https://github.com/dui-w-moonrin/amazon-books-de-databricks/raw/main/images/timeline.png)

## 📊 Data Studio (Looker Studio) Demo
![Demo](https://github.com/dui-w-moonrin/amazon-books-de-databricks/raw/main/images/bi_screenshot.png)

[Demo](https://datastudio.google.com/s/o4eNN6GTweM)

## Connect with me
- [LinkedIn](https://www.linkedin.com/in/dui-w-moonrin/)
- [Credly](https://www.credly.com/users/dui-w-moonrin/)
