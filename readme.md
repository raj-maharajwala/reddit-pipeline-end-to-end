# Reddit Data Pipeline 0->1

## Architecture Summary

This project implements an end-to-end ETL pipeline for Reddit data. It begins by extracting data from the Reddit API, orchestrates execution with Apache Airflow and Celery, stages data locally, stores raw data in Amazon S3, transforms and catalogs it using AWS Glue and Athena, and finally loads curated data into Amazon Redshift for analytics and reporting.

## Table of Contents

- [End-to-End Workflow](#end-to-end-data-engineering-workflow)
- [Prerequisites](#prerequisites)
- [Setup](#system-setup)
- [Future Enhancements](#future-enhancements)

## End-to-End Data Engineering Workflow
![RedditDataEngineering.png](assets%2FRedditDataEngineering.png)

## End-to-End Data Engineering Workflow

| Step | Data Engineering Layer | Component | Purpose | Input | Output |
|------|-------------------------|-----------|---------|-------|--------|
| 1 | Source | Reddit API | Extract raw Reddit posts and metadata from the target subreddit. | Subreddit name + API credentials | Raw Reddit response data |
| 2 | Orchestration | Apache Airflow + Celery | Schedule, coordinate, and execute the pipeline as a repeatable workflow. | DAG definition + runtime configuration | Managed task execution |
| 3 | Metadata Backend | PostgreSQL | Persist workflow state, task metadata, and Airflow backend information. | DAG runs, task states, logs metadata | Orchestration metadata store |
| 4 | Extraction & Structuring | Python ETL | Parse API responses and convert semi-structured data into a tabular dataset. | Raw Reddit objects / JSON-like responses | Structured records |
| 5 | Staging | Local CSV | Materialize intermediate pipeline output before uploading to cloud storage. | Structured records | Local staged dataset |
| 6 | Raw Data Lake Storage | Amazon S3 | Store extracted raw data durably for downstream processing. | Local CSV / extracted dataset | Raw data in object storage |
| 7 | Transformation | AWS Glue | Clean, reshape, and prepare raw data into analytics-ready form. | Raw S3 dataset | Transformed dataset |
| 8 | Metadata Catalog | AWS Glue Data Catalog | Register dataset schema so downstream SQL engines can discover and query it. | Transformed files in S3 | Cataloged tables / metadata |
| 9 | Query Layer | Amazon Athena | Run SQL on top of cataloged S3 data for validation and exploratory analysis. | Cataloged transformed data | Query results |
| 10 | Data Warehouse | Amazon Redshift | Load curated data into an analytical warehouse optimized for reporting and dashboarding. | Transformed dataset | Analytics-ready warehouse tables |
| 11 | Consumption | SQL / Analytics | Enable downstream analysis, reporting, and decision support. | Curated Redshift tables | Insights and dashboards |

## Prerequisites
- Reddit API credentials.
- AWS Account with appropriate permissions for S3, Glue, Athena, and Redshift.
- Docker Installation
- Python 3.9 or higher

## Setup
### Clone + Environment Setup

```bash
git clone https://github.com/airscholar/RedditDataEngineering.git # Clone repository
cd RedditDataEngineering                                           # Navigate to project

python3 -m venv venv                                               # Create virtual environment
source venv/bin/activate                                           # Activate virtual environment

pip install -r requirements.txt                                    # Install dependencies
```

### Configuration + Docker Setup

```bash
mv config/config.conf.example config/config.conf           # Create config file from template

# Add Reddit API credentials to config/config.conf

docker-compose up -d                                       # Start all containers

open http://localhost:8080                                 # Open Airflow UI
```



## Future Enhancements
Potential next-step improvements for an enterprise-grade version of this pipeline could include:
- Streaming ingestion for near real-time data movement
- Stronger data quality validation and automated checks
- Data lineage and governance for better discoverability
- Observability and SLA monitoring for production reliability
- Semantic modeling for cleaner downstream analytics