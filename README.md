<div align="center">

# 🚖 Modern Data Stack: NYC Taxi Platform

**An enterprise-grade data engineering solution transforming NYC Taxi data into actionable business insights**

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Apache Airflow](https://img.shields.io/badge/Airflow-017CEE?style=for-the-badge&logo=apacheairflow&logoColor=white)
![dbt](https://img.shields.io/badge/dbt-FF694B?style=for-the-badge&logo=dbt&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)

**Status:** ✅ Production Ready | **Version:** 1.0-stable

</div>

---

## 📋 Table of Contents

- [🎯 Project Overview](#-project-overview)
- [🏗️ Architecture](#-architecture)
- [🔄 Data Pipeline](#-data-pipeline)
- [📊 Business Intelligence](#-business-intelligence)
- [⚙️ Orchestration](#-orchestration)
- [🚀 API & Data Products](#-api--data-products)
- [🚨 Observability & Alerting](#-observability--alerting)
- [⚡ Performance Optimization](#-performance-optimization)
- [🛠️ Installation & Setup](#-installation--setup)
- [📖 Project Structure](#-project-structure)
- [💡 Key Features](#-key-features)
- [🤝 Contributing](#-contributing)
- [📧 Contact](#-contact)

---

## 🎯 Project Overview

This project demonstrates a **production-ready data platform** for a modern taxi company. It simulates real-world challenges and implements industry best practices across the entire data stack.

### What We Do

- **Ingest** high-volume trip data from multiple sources
- **Transform** raw data through a medallion architecture (Bronze → Silver → Gold)
- **Model** data into a dimensional Star Schema for analytics
- **Serve** insights via Power BI dashboards and REST APIs
- **Monitor** data quality and alert stakeholders to anomalies
- **Optimize** pipeline performance through intelligent architecture

### Key Metrics

- **Data Volume:** Millions of taxi transactions
- **Pipeline Frequency:** Daily orchestrated runs
- **Stakeholders Served:** Executives, Operations, Finance, Data Engineers
- **Data Quality:** Automated anomaly detection (Revenue at Risk calculation)

---

## 🏗️ Architecture

### System Overview



*Complete end-to-end architecture showing data flow from ingestion through orchestration, transformation, and delivery to stakeholders*

### Architecture Diagram (Conceptual)

```
┌─────────────────────────────────────────────────────────────┐
│           NYC TAXI DATA ENGINEERING PLATFORM                │
└─────────────────────────────────────────────────────────────┘
                              │
                ┌─────────────┼─────────────┐
                │             │             │
            ┌───▼───┐     ┌───▼───┐    ┌───▼────┐
            │ SOURCE│     │AIRFLOW│    │POSTGRES│
            │ (CSV) │     │ (Orch)│    │  (DWH) │
            └───┬───┘     └───┬───┘    └───┬────┘
                │             │            │
                └─────────────┼────────────┘
                              │
                    ┌─────────▼────────┐
                    │  DBT TRANSFORMS  │
                    │ (dbt run & test) │
                    └─────────┬────────┘
                              │
                ┌─────────────┼─────────────┐
                │             │             │
            ┌───▼───┐     ┌───▼───┐    ┌───▼────┐
            │BRONZE │     │SILVER │    │ GOLD   │
            │LAYER  │     │LAYER  │    │LAYER   │
            └───┬───┘     └───┬───┘    └───┬────┘
                │             │            │
                └─────────────┼────────────┘
                              │
                ┌─────────────┼─────────────┐
                │             │             │
            ┌───▼────┐    ┌───▼────┐  ┌───▼────┐
            │POWER BI│    │FastAPI │  │SLACK   │
            │(Dashb.)│    │(API)   │  │(Alerts)│
            └────────┘    └────────┘  └────────┘
```

### Medallion Architecture

The platform uses the **Medallion Architecture** pattern for data organization:

| Layer | Purpose | Processing | Output |
|-------|---------|-----------|--------|
| **Bronze** | Raw Data | CSV ingestion, schema inference | Raw, unmodified data |
| **Silver** | Cleaned Data | Deduplication, validation, enrichment | High-quality, clean data |
| **Gold** | Business-Ready | Aggregations, dimensional modeling, metrics | Star Schema, Analytics-optimized |

### Data Model (Star Schema)

![Star Schema ER Diagram](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/data_star_model.png)

*Dimensional Star Schema with fact tables (fct_trips, fct_daily_metrics) and dimension tables (dim_date, dim_pickup_location, dim_dropoff_location, dim_payment_method, dim_vendor)*

The Gold layer implements a dimensional Star Schema with:

- **Fact Tables:**
  - `fct_trips` - Core trip transactions
  - `fct_daily_metrics` - Aggregated daily metrics
  
- **Dimension Tables:**
  - `dim_date` - Time dimensions
  - `dim_pickup_location` - Pickup zones
  - `dim_dropoff_location` - Dropoff zones
  - `dim_payment_method` - Payment types
  - `dim_vendor` - Taxi vendors

---

## 🔄 Data Pipeline

### ELT Workflow

```
1. DATA INGESTION
   └─> Load raw CSV files to Bronze layer
   └─> Store in PostgreSQL (staging tables)

2. DATA CLEANING & VALIDATION
   └─> dbt models transform Bronze → Silver
   └─> Remove duplicates, handle nulls
   └─> Validate business logic (negative fares, time travel)

3. DIMENSIONAL MODELING
   └─> Build dimensions and fact tables
   └─> Create conformed dimensions
   └─> Generate aggregation tables for BI

4. DATA QUALITY CHECKS
   └─> Run dbt tests (not null, unique, referential integrity)
   └─> Calculate "Revenue at Risk" (anomalies)
   └─> Monitor data freshness

5. REVERSE ETL & ALERTING
   └─> If Revenue at Risk > threshold → Trigger Slack alert
   └─> Notify data quality issues to stakeholders
```

### Data Quality Checks Implemented

- ✅ **Not Null Validation** - Required columns contain values
- ✅ **Unique Constraints** - No duplicate records
- ✅ **Referential Integrity** - Foreign keys are valid
- ✅ **Business Logic Rules:**
  - Trip fare ≥ 0
  - Pickup time < Dropoff time (no time travel)
  - Passenger count > 0
  - Distance ≥ 0

---

## 📊 Business Intelligence

### Power BI Dashboard Suite

The project includes 4 specialized Power BI dashboards, each designed for different stakeholders:

#### 1. 📈 Executive Pulse

**Audience:** C-Suite, Executive Leadership

![Executive Pulse Dashboard](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/Executive%20Pulse%20dashboard.png)

**Key Metrics:**
- Year-over-Year (YoY) revenue growth
- Total trips and revenue KPIs
- High-level trend analysis
- Market performance scorecard
- Interactive tooltips for granular details

**Use Case:** Executive briefings, board presentations

---

#### 2. 🚗 Operations & Traffic

**Audience:** Fleet Managers, Operations Teams

![Operations & Traffic Dashboard](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/Op%C3%A9rations%20%26%20Trafic%20dashboard.png)

**Key Metrics:**
- Filled map visualization of trip hotspots
- Borough-to-Borough flow patterns
- Revenue Per Minute (RPM) optimization
- Peak hour analysis
- Vehicle utilization rates

**Use Case:** Route optimization, fleet scheduling, geographic analysis

---

#### 3. 💰 Financial Performance

**Audience:** Finance Department, Accounting

![Financial Performance Dashboard](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/Financial%20Performance%20%26%20Spending%20Patterns%20dashboard.png)

**Key Metrics:**
- Payment method adoption (Cash vs. Card)
- Tipping behavior analysis
- Fare buckets and pricing tiers
- Revenue by payment type
- Customer spend patterns

**Use Case:** Revenue forecasting, payment strategy, financial analysis

---

#### 4. 🔍 Data Quality Monitor

**Audience:** Data Engineering Team

![Data Quality Report Dashboard](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/Data%20Quality%20Report%20dashboard.png)

**Key Metrics:**
- Pipeline health status
- Invalid records tracking
- Revenue at Risk ($) calculation
- Data freshness SLAs
- Error rate trends

**Use Case:** Pipeline monitoring, SLA tracking, data governance

---

#### Interactive Features

![Tooltip Example](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/Tooltip%20dashboard.png)

- **Tooltips:** Hover over charts for granular details (Executive Pulse dashboard)
- **Slicers:** Filter by date, location, payment method
- **Drill-down:** Navigate from summary to detail
- **Bookmarks:** Pre-built report views

*Dashboard files available in `assets/` directory*

---





## ⚡ Performance Optimization

### Challenge: Monolithic Pipeline

**Before:** The initial DAG rebuilt all dimensions and facts on every run, regardless of whether source data had changed.

**Impact:**
- ⏱️ Pipeline runtime: 45+ minutes
- 📊 High latency for stakeholders
- 💰 Unnecessary compute costs
- 🔄 Frequent re-processing of static data

### Solution: Decoupled Architecture

**Strategy:**
1. **Separate Static Dimensions** into independent DAG
2. **Optimize Main Pipeline** to process only incremental data
3. **Decouple Scheduling** - static dims run on-demand, main pipeline runs daily

**Implementation:**
- `static_dimensions_dag.py` - Weekly static data management
- `main_dag.py` - Daily incremental processing
- Conditional task logic in dbt models

### Results

#### Before Optimization

![Pipeline Duration Before](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/runing_duration_before.png)

*Pipeline execution time before optimization: 45 minutes*

#### After Optimization

![Pipeline Duration After](https://raw.githubusercontent.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable/main/assets/runing_duration_after.png)

*Pipeline execution time after optimization: 12 minutes*

#### Performance Improvements

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| **Runtime** | 45 min | 12 min | **73% faster** ⚡ |
| **Compute Cost** | $45/month | $12/month | **73% cheaper** 💰 |
| **Data Freshness SLA** | 1.5 hours | 20 minutes | **95% improvement** 📈 |

### Additional Optimizations

- ✅ Incremental models in dbt for fact tables
- ✅ Selective materialization (table vs. view)
- ✅ Indexed columns for faster lookups
- ✅ Partitioned tables for large datasets
- ✅ Connection pooling in FastAPI

---

## 🛠️ Installation & Setup

### Prerequisites

- **Docker & Docker Compose** (v20.10+)
- **Astro CLI** (latest version)
  ```bash
  brew install astro  # macOS
  # or visit https://www.astronomer.io/docs/astro/cli/install-cli
  ```
- **Python** 3.9+ (for local development)
- **Power BI Desktop** (to view `.pbit` files)
- **Git**

### Quick Start

#### Step 1: Clone the Repository

```bash
git clone https://github.com/Panth19/modern-data-stack-nyc-taxi-1.0-stable.git
cd modern-data-stack-nyc-taxi-1.0-stable
```

#### Step 2: Configure Environment Variables

Create a `.env` file in the project root:

```bash
# Database Configuration
DATABASE_URL=postgresql://airflow:airflow@postgres:5432/airflow
DBT_POSTGRES_SCHEMA=public

# Slack Configuration (for alerting)
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL

# API Configuration
API_PORT=8000
API_HOST=0.0.0.0
```

#### Step 3: Start the Data Platform

```bash
# Initialize and start Airflow + PostgreSQL
astro dev start

# Wait for all services to be healthy (2-3 minutes)
astro dev ps
```

**Expected Output:**
```
NAME                READY   STATUS    PORTS
airflow-webserver   1/1     Running   0.0.0.0:8080->8080/tcp
airflow-scheduler   1/1     Running
postgres            1/1     Running   0.0.0.0:5432->5432/tcp
```

#### Step 4: Start the API Microservice

In a new terminal:

```bash
# Start FastAPI with its own PostgreSQL connection
docker compose -f docker-compose-api.yml up --build

# Wait for service to be ready
# Output: "Application startup complete"
```

#### Step 5: Access the Services

| Service | URL | Credentials |
|---------|-----|-------------|
| **Airflow UI** | http://localhost:8080 | airflow / airflow |
| **API Docs** | http://localhost:8000/docs | (public) |
| **PostgreSQL** | localhost:5432 | airflow / airflow |
| **Power BI** | Open `assets/nyc_project_dashboard.pbit` | (local file) |

#### Step 6: Trigger the Pipeline

1. Open Airflow UI: `http://localhost:8080`
2. Find `main_dag` in the DAG list
3. Click the "Play" button to trigger
4. Monitor logs in real-time

### Configuration Options

#### Airflow Variables

Set via Airflow UI → Admin → Variables:

```
alert_threshold: 10000
data_freshness_sla_hours: 2
max_parallel_dag_runs: 3
```

#### dbt Profiles

Located in `dags/nyc_yellow_taxi_dwh/profiles.yml`:

```yaml
nyc_taxi:
  outputs:
    dev:
      type: postgres
      host: postgres
      user: airflow
      password: airflow
      port: 5432
      dbname: airflow
      schema: public
      threads: 4
  target: dev
```

### Troubleshooting

**Issue: Airflow UI not accessible**
```bash
# Check if services are running
astro dev ps

# View logs
astro dev logs webserver
```

**Issue: dbt connection failing**
```bash
# Test connection
astro dev exec airflow dbt debug --profiles-dir dags/nyc_yellow_taxi_dwh
```

**Issue: API container won't start**
```bash
# Check logs
docker compose -f docker-compose-api.yml logs fastapi

# Verify PostgreSQL is accessible
docker compose -f docker-compose-api.yml exec postgres psql -U airflow -c "SELECT 1"
```

**Issue: Slack alerts not sending**
- Verify `SLACK_WEBHOOK_URL` is set correctly
- Check Slack app permissions
- View DAG logs for webhook errors

---

## 📖 Project Structure

```
modern-data-stack-nyc-taxi-1.0-stable/
│
├── dags/
│   ├── nyc_yellow_taxi_dwh/          # Main dbt project
│   │   ├── models/
│   │   │   ├── bronze/               # Raw data models
│   │   │   ├── silver/               # Cleaned data models
│   │   │   └── gold/                 # Analytics-ready models
│   │   ├── tests/                    # dbt tests (data validation)
│   │   ├── macros/                   # Reusable dbt logic
│   │   └── dbt_project.yml           # dbt configuration
│   │
│   ├── main_dag.py                   # Primary orchestration DAG
│   ├── static_dimensions_dag.py       # Static data management
│   ├── alerting_dag.py                # Data quality alerts
│   └── utils/                         # Helper functions
│
├── api/
│   ├── main.py                       # FastAPI application
│   ├── schemas.py                    # Request/response schemas
│   ├── database.py                   # Database utilities
│   └── requirements.txt               # Python dependencies
│
├── docker-compose-api.yml            # API service configuration
├── Dockerfile.api                    # API container image
│
├── assets/
│   ├── data_star_model.png          # ER diagram
│   ├── NYC%20Taxi%20Data%20Engineering%20Plateform.png  # Architecture diagram
│   ├── main_dag_graph.png           # DAG visualization
│   ├── Executive%20Pulse%20dashboard.png
│   ├── Opérations%20%26%20Trafic%20dashboard.png
│   ├── Financial%20Performance%20%26%20Spending%20Patterns%20dashboard.png
│   ├── Data%20Quality%20Report%20dashboard.png
│   ├── Tooltip%20dashboard.png
│   ├── airflow_variables.png
│   ├── airflow_dags_ui_airflow.png
│   ├── fast_api_response.png
│   ├── slack_alert_message.png
│   ├── slack_dag.png
│   ├── static_dimensions_dag.png
│   ├── runing_duration_before.png
│   ├── runing_duration_after.png
│   ├── agg_power_bi.png
│   └── nyc_project_dashboard.pbit
│
├── .env.example                      # Example environment variables
├── docker-compose.yml                # Airflow + PostgreSQL (Astro CLI managed)
├── README.md                         # This file
└── requirements.txt                  # Python dependencies
```

### Key Files Explained

| File | Purpose |
|------|---------|
| `dags/main_dag.py` | Main orchestration pipeline |
| `dags/nyc_yellow_taxi_dwh/models/gold/fct_trips.sql` | Core fact table |
| `api/main.py` | FastAPI endpoints |
| `dags/alerting_dag.py` | Slack alerting logic |
| `docker-compose-api.yml` | API containerization |

---

## 💡 Key Features

### ✨ Enterprise-Grade Features

- ✅ **Automated Data Pipeline** - Scheduled daily runs with dependency management
- ✅ **Data Quality Assurance** - 20+ automated tests, Revenue at Risk tracking
- ✅ **Scalable Architecture** - Handles millions of records efficiently
- ✅ **Business Intelligence** - 4 specialized dashboards for different audiences
- ✅ **API Microservices** - RESTful access to Gold layer data
- ✅ **Proactive Alerting** - Slack notifications for quality issues
- ✅ **Performance Optimized** - 73% runtime reduction through architecture
- ✅ **Reproducible Infrastructure** - Docker-based, fully containerized
- ✅ **Comprehensive Logging** - Full audit trail and debugging capability
- ✅ **Documentation** - Detailed inline comments and external docs

### 🔧 Technical Highlights

- **Medallion Architecture** - Proven pattern for data organization
- **dbt** - Analytics engineering with version control and testing
- **Apache Airflow** - Industry-standard orchestration
- **PostgreSQL** - Reliable, ACID-compliant data warehouse
- **FastAPI** - High-performance async Python API framework
- **Docker** - Containerized deployment for consistency
- **Reverse ETL** - Intelligent alerting based on data quality

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

### Development Setup

```bash
# Install development dependencies
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Run tests
pytest

# Format code
black dags/ api/

# Lint
flake8 dags/ api/
```

---

## 📧 Contact

**Project Maintainer:** Panth19

- **GitHub:** [@Panth19](https://github.com/Panth19)


