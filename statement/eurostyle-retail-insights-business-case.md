# EuroStyle Retail – Insights with Databricks & Fabric

## Business Case

EuroStyle is a European online fashion retailer operating in the UK, France, Germany, and the Benelux.  
The company has grown steadily over the last decade, reaching more than **200.000 customers** and offering **over 50.000 SKUs** (SKU represents a distinct product and variation) in apparel, footwear, and accessories.  

Despite its growth, EuroStyle is facing several challenges:
- **Eroding margins** caused by high return rates, particularly in Germany and the Benelux.  
- **Fragmented data sources** stored across different files and systems, preventing a unified customer view.  
- **Lack of reliable KPIs** (GMV, AOV, margin %) with inconsistent Excel-based reporting.  
- **Customer churn risk** suspected but not measured or predicted.  
- **Slow decision-making**: business questions take days to answer because analysts manually combine multiple files.  

The **Chief Marketing Officer (CMO)** has tasked the Data & AI team to deliver a prototype of a **modern data platform** that provides both **business insights** and **predictive capabilities**.

---

## Project Objectives

1. **Data Engineering**:  
   - Build a reproducible **Medallion architecture** (Bronze → Silver → Gold) in Databricks Free Edition (https://www.databricks.com/learn/free-edition).  
   - Ensure **data quality, reliability, and versioning** using Delta Lake.  

2. **Data Analytics**:  
   - Provide executives with **dashboards** that show sales performance (GMV, AOV, margin, return rates).  
   - Create a **Customer 360° view** linking transactions, demographics, and engagement.  

3. **Data Science**:  
   - Engineer features such as **RFM (Recency, Frequency, Monetary)** and product diversity.  
   - Develop **machine learning models** to predict churn and estimate customer value.  
   - Track all experiments with **MLflow** and publish churn scores back into the Gold layer.  

4. **Integration with Fabric**:  
   - Simulate enterprise consumption of insights by **exporting Gold tables from Databricks Free Edition** to Microsoft Fabric.  
   - Ingest the data into a **Fabric Lakehouse** and expose it in **Power BI dashboards**.  
   - Demonstrate modern data platform integration patterns.  

---

## Profiles and Responsibilities

This project involves **three complementary roles**:

- **Data Engineers (DE)**  
  - Own the **Bronze, Silver, Gold pipeline**.  
  - Ensure ingestion, cleaning, transformations, and data quality.  
  - Deliver the foundation upon which analytics and ML can operate.  

- **Data Analysts (DA)**  
  - Start exploring **raw and Bronze data immediately** to build first KPIs.  
  - Create and refine dashboards on **Silver and Gold tables**.  
  - Define the business logic for metrics (e.g., churn definition, segmentation).  

- **Data Scientists (DS)**  
  - Build features from Silver and Gold tables.  
  - Define predictive targets (churn, spend, lifetime value).  
  - Train and compare models, track them in **MLflow**, and publish scores back into Gold.  

👉 Collaboration is mandatory:  
- Analysts raise questions → Engineers implement data cleaning rules.  
- Engineers provide cleaned Silver data → Scientists use it for feature engineering.  
- Scientists provide churn predictions → Analysts visualize them for business decision-making.  



## Agile Way of Working

The project follows **Agile principles** with short, iterative sprints:

- **Sprint Planning (2h)**: define goals and select must-have / stretching / excellence tasks.  
- **Daily Stand-up (10 min)**: each student answers  
  1. What did I do yesterday?  
  2. What will I do today?  
  3. What are my blockers?  
- **Sprint Review (30 min)**: demo of dashboards, Delta tables, MLflow runs.  
- **Sprint Retrospective (30 min)**: reflect on what worked and what can be improved.  

## Sprint Structure

| Sprint       | Duration | Focus                       | Key Deliverables                                                                                                                                     |
| ------------ | -------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sprint 0** | 0.5 day  | Setup & Agile Planning      | Databricks Free Edition workspace, Fabric tenant, Azure DevOps Board setup, assign team roles, read EuroStyle case, creation of epics, features, stories, and work items |
| **Sprint 1** | 4.5 days | Bronze & First Insights     | Bronze ingestion (Delta tables with metadata), initial KPIs on raw data (GMV, AOV, orders per customer), 1-page "first look" dashboard               |
| **Sprint 2** | 4.5 days | Silver & Business Logic     | Clean Silver tables (deduplication, normalized schema, joins), define churn logic (e.g., no order in 90 days), dashboard comparing Raw vs Silver data|
| **Sprint 3** | 4.5 days | Gold & Features             | Gold marts (`sales_daily`, `category_perf`, `customer_360`), Executive dashboard, Customer 360 table enriched with RFM and additional features       |
| **Sprint 4** | 4.5 days | MLflow & Fabric Integration | Train and log ML models in MLflow, batch churn scoring into Gold, export Gold → Fabric Lakehouse, Power BI dashboards (Executive + Customer Risk)    |



## Roles per Sprint

- **Data Engineers (DE)**: ingestion, cleaning, transformations, pipeline rebuild, export to Fabric Lakehouse.  
- **Data Analysts (DA)**: KPIs on raw & Silver, dashboards in Databricks + Power BI (Executive, Customer 360, Customer Risk).  
- **Data Scientists (DS)**: feature engineering, churn logic, model training in MLflow, scoring into Gold, model monitoring.  

> **Collaboration is continuous:**  
> - Analysts raise data quality issues → Engineers fix in Silver.  
> - Scientists generate churn scores → Analysts integrate into dashboards.  
> - Engineers keep pipeline reproducible → all profiles can rely on clean data.  



## Deliverables by Profile

### Must-Have (non-negotiable)
- **Data Engineers (DE)**
  - Build Bronze → Silver → Gold pipeline in Databricks Free Edition using manual notebooks.
  - Bronze: raw CSV ingestion into Delta with metadata (`ingest_ts`, `source_file`, `data_quality_flags`).
  - Silver: implement data cleaning rules, deduplication logic, schema standardization.
  - Gold: create 3 business marts (`sales_daily`, `product_performance`, `customer_360`).
  - Document pipeline with clear rebuild instructions and data lineage.
  - **Fabric integration**: Export Gold tables to Fabric Lakehouse via Parquet files.
- **Data Analysts (DA)**
  - Build 2 interactive dashboards in Databricks notebooks (using plotly/matplotlib):
    - Executive KPI dashboard (GMV trends, AOV by country, return rates).
    - Customer segmentation analysis (RFM distribution, cohort retention).
  - Perform data quality analysis comparing Raw vs Silver metrics.
  - Define business rules for churn (present findings to DS team).
  - **Fabric integration**: Export key visualizations to Power BI for stakeholder consumption.
- **Data Scientists (DS)**
  - Engineer 5+ features from Silver/Gold tables (RFM, seasonality, basket diversity).
  - Define and validate churn target using business logic from DA team.
  - Train 2 baseline models in MLflow (Logistic Regression + Random Forest).
  - Generate churn scores and integrate back into Gold layer.

---

### Stretching (ambitious)
- **Data Engineers (DE)**
  - Implement automated data quality checks with custom assertions (row counts, uniqueness, business rules).
  - Create Delta Lake time-travel demo with version history and rollback capabilities.
  - Build a "data catalog" notebook documenting all tables, schemas, and dependencies.
- **Data Analysts (DA)**
  - Develop advanced cohort analysis and customer lifetime value calculations.
  - Create interactive drill-down capabilities in dashboards (country → region → customer).
  - Perform A/B testing simulation on discount campaigns using historical data.
- **Data Scientists (DS)**
  - Implement advanced feature engineering (seasonal patterns, customer similarity).
  - Train and compare 4+ models (XGBoost, LightGBM) with hyperparameter tuning.
  - Create model interpretability analysis (SHAP values, feature importance).
  - Build a simple model monitoring system tracking drift in input features.

---

### Excellence (mastery)
- **Data Engineers (DE)**
  - Create a complete pipeline orchestration notebook that rebuilds everything from scratch.
  - Implement incremental data loading strategies for new data batches.
  - Build automated export pipeline from Databricks to Fabric Lakehouse with scheduling documentation.
- **Data Analysts (DA)**
  - Build comprehensive Power BI dashboards in Fabric with advanced visualizations.
  - Create customer risk scoring dashboard integrating ML predictions from DS team.
  - Develop recommendation system for high-value customer retention strategies.
  - Implement real-time dashboard refresh patterns from Fabric Lakehouse.
- **Data Scientists (DS)**
  - Implement end-to-end MLOps pipeline (training, validation, scoring, monitoring).
  - Perform batch inference on all customers and create risk segmentation.
  - Build a simple recommendation engine based on customer behavior patterns.
  - Create automated model retraining logic based on performance degradation.

---

### Hire Me Level (CV Candy / Optional)
- **Data Engineers (DE)**
  - Deploy pipeline as containerized solution with Docker + GitHub Actions.
  - Create data lineage visualization using networkx or similar.
  - Write technical blog post about "Building Medallion Architecture on a Budget".
- **Data Analysts (DA)**
  - Build interactive Streamlit dashboard for business stakeholders.
  - Create animated visualizations showing EuroStyle's growth story.
  - Develop presentation skills with recorded business case presentation.
- **Data Scientists (DS)**
  - Deploy churn prediction as REST API using FastAPI or Flask.
  - Create AutoML comparison study (traditional ML vs AutoML tools).
  - Write case study: "From Spreadsheets to AI: EuroStyle's Data Transformation".
- **Any profile**
  - LinkedIn optimization with project showcase and learnings summary.
  - GitHub repository with professional README, documentation, and project structure.

---

## Dataset  

You will work with **European retail datasets from Kaggle**:

- **Online Retail II (UCI – UK Retailer)**  
  [https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)  

- Alternative (multitable, synthetic but realistic):  
  **European Fashion Store – Multitable Dataset**  
  [https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset](https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset)  

Students must download the CSV files and upload them into Databricks Free Edition under `/FileStore/retail/raw/...`.

---
