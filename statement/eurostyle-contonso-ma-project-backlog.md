# EuroStyle Project - Product Backlog (Databricks & Fabric)

## Backlog Structure
This backlog follows Agile methodology with hierarchical organization:
- **Epics**: Major business capabilities 
- **Features**: Functional components within epics
- **User Stories**: Specific user needs and outcomes
- **Tasks**: Technical implementation items

## Learning Resources & Documentation

### Databricks Essential Concepts
- **[Databricks Free Edition Guide](https://docs.databricks.com/getting-started/free-edition.html)** - Setup and limitations
- **[Delta Lake Architecture](https://docs.databricks.com/delta/index.html)** - Modern data lake storage
- **[Medallion Architecture](https://docs.databricks.com/lakehouse/medallion.html)** - Bronze/Silver/Gold pattern
- **[PySpark DataFrame API](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html)** - Data transformation
- **[MLflow Tracking](https://mlflow.org/docs/latest/tracking.html)** - ML experiment management

### Microsoft Fabric Resources
- **[Fabric Lakehouse Overview](https://learn.microsoft.com/en-us/fabric/data-engineering/lakehouse-overview)** - Data storage concepts
- **[Power BI Service](https://learn.microsoft.com/en-us/power-bi/fundamentals/)** - Dashboard creation
- **[Fabric Data Pipelines](https://learn.microsoft.com/en-us/fabric/data-factory/)** - Data orchestration

### Data Engineering Best Practices
- **[Data Quality Framework](https://docs.databricks.com/lakehouse/data-quality.html)** - Validation strategies
- **[Schema Evolution](https://docs.databricks.com/delta/delta-batch.html#table-schema-evolution)** - Managing changing data
- **[Performance Optimization](https://docs.databricks.com/optimizations/index.html)** - Query tuning techniques

### Orchestration & Workflow Management
- **[Apache Airflow](https://airflow.apache.org/docs/)** - Workflow orchestration and scheduling
- **[Airflow with Databricks](https://docs.databricks.com/workflows/jobs/airflow.html)** - Integration patterns

---

## Datasets & Bridge Overview (M&A single source of truth)

- Datasets (dual-source to simulate M&A):
  - EuroStyle: Online Retail II (UCI) – single-table transactional retail (orders/lines).
  - Contoso: European Fashion Store – multi-table synthetic retail (orders, customers, products, stores).
  - Rationale: forces schema harmonization (codes, currencies, tax rules) and enables side-by-side vs unified analytics.
- Usage across platforms:
  - Databricks (Free Edition): Ingest both datasets into separate Bronze zones (eurostyle_bronze, contoso_bronze) → clean to Silver with mapping tables and currency normalization → produce unified Gold marts.
  - Fabric: Early DA prototypes can connect to Databricks for S1–S2; from S3, Fabric serves the unified Gold via Lakehouse (Direct Lake) for governance and performance.

### Databricks ↔ Fabric Bridge: Release Contract (unified Gold)
- Release root (storage-agnostic logical path):
  - release/gold/{table}/{YYYY}/{MM}/{DD}/v{major.minor.patch}/
- File format & naming:
  - Parquet files: {table}_{YYYYMMDD}_v{major.minor.patch}_part-*.parquet
  - Success marker: _SUCCESS written last
  - Manifest (JSON): release_manifest.json with fields:
    - dataset: "eurostyle-contoso-unified", table: string, version: semver, run_id: uuid, created_utc: ISO8601
    - row_count_total: long, files: [{ name, rows, md5 }]
    - schema: { columns: [{ name, type }], schema_hash: sha256(normalized_ddl) }
    - watermark: { date_max: YYYY-MM-DD, load_window: { start, end } }
- Versioning rules:
  - Backward-compatible change → bump minor (e.g., add nullable column)
  - Breaking change → bump major (e.g., rename, type change)
- Atomicity:
  - Write into a _staging folder, then move/rename to final; write _SUCCESS last.
- Quality gates before publish:
  - Non-zero rows, primary/business keys non-null, duplicate check passed, schema_hash match
- SLAs:
  - Daily drop by 15:00 UTC, keep last 7 days, idempotent re-loads allowed

### Fabric Ingestion of the Release (primary orchestration = Fabric Data Pipelines)
- Per table steps: Get Metadata (detect new version) → Validate manifest → Copy/Load Parquet → Create/Replace Delta in Lakehouse → Refresh/Update semantic model.
- Partitioning: by Date (YYYY/MM/DD) to support Incremental Refresh/Direct Lake.
- If direct automation from Databricks Free isn't possible, do a manual file handoff (export Parquet + manifest locally, then Upload to Fabric Lakehouse Files). The contract ensures repeatability.

---

## Free vs Paid (Databricks & Fabric) — capabilities and impact

| Area | Free/Trial | Paid/Pro | Impact on solution |
|---|---|---|---|
| Databricks compute | 1 small all-purpose cluster; idle timeout ~2h | Multiple clusters, autoscaling | Early sprints OK; plan short sessions; pre-aggregate for DA |
| Scheduling | No Jobs/Workflows | Jobs/Workflows with retries/params/APIs | Use Fabric Pipelines or manual runs on Free; full E2E automation on Paid |
| SQL access | No SQL Warehouse; connect PBI to cluster via PAT | SQL Warehouses for BI | S1–S2: PBI DirectQuery/Import to cluster; Paid: stable, governed BI |
| Governance | No Unity Catalog | Unity Catalog | On Paid, centralize schemas/permissions/lineage |
| ML Ops | MLflow tracking only | Model registry/serving (workspace limits apply) | Keep "MLOps-lite" on Free; expand on Paid |
| Fabric BI | Authoring, limited sharing | Pro/Premium: governance, pipelines | From S3, host semantic model in Fabric; Paid enables Dev/Test/Prod |
| Data bridge | Manual/file-based contract works | ADLS Gen2 + Shortcuts/Pipelines | Same contract; Paid simplifies with scheduled pipelines |

---

### Phase / Platform decision matrix (DA path)

| Phase/Sprint | Source of truth | DA connection recommended | Why |
|---|---|---|---|
| S0–S1 (Bronze) | Databricks Bronze (EuroStyle + Contoso) | Power BI → DirectQuery (or Import) to Databricks cluster (PAT) | Fast start; First Look KPIs on raw |
| S2 (Silver) | Databricks Silver (harmonized) | Power BI → DirectQuery to cluster; optional Composite model | Compare Raw vs Silver while harmonizing |
| S3 (Gold) | Databricks Gold (unified marts) | Switch to Fabric: Direct Lake + semantic model | Performance, governance, RLS, deployment pipelines |
| S4 (ML + deploy) | Fabric Lakehouse (Gold + scores) | Fabric / Power BI (Direct Lake; Composite if needed) | Executive suite, certified datasets |

Golden rule: Before Gold → DA on Databricks; From Gold → DA on Fabric.

Power BI → Databricks on Free (S1–S2):
1) Start cluster; create PAT; copy Hostname + cluster HTTP Path.
2) Ensure hive_metastore tables/views exist (externals over Bronze/Silver if needed).
3) Power BI Desktop → Get Data → Databricks; mode = DirectQuery or Import; Auth = PAT.
4) Build visuals; expect slower DirectQuery; prefer pre-aggregations/views in Databricks.

## Epic 1 - Data Foundation Platform
**Goal**: Build a robust, scalable data platform with Medallion architecture (Bronze → Silver → Gold).

### Feature 1.1: Data Ingestion (Sprint 1)
- **User Story 1.1.1**: As a Data Engineer, I want to ingest raw CSVs into a Bronze Delta layer so that downstream processing has a reliable source.  
  - **Learning Resources**: Databricks Free Edition Guide, Delta Lake Architecture, PySpark DataFrame API
  - **Tasks**:  
    - Setup Databricks workspace + foldering  
    - Bronze ingestion notebooks with schema inference  
    - Add metadata columns (`ingest_ts`, `source_file`)  
    - Validate schema integrity  

### Feature 1.2: Data Quality Monitoring (Sprint 1)
- **User Story 1.2.1**: As a Data Engineer, I want automated quality checks so issues are caught early.  
  - **Learning Resources**: Data Quality Framework, PySpark DataFrame API, Performance Optimization
  - **Tasks**:  
    - Define data quality rules (row counts, nulls)  
    - Implement validation logic (PySpark filters/asserts)  
    - Track metrics in a quality log Delta table  

### Feature 1.3: Silver Data Cleaning (Sprint 2)
- **User Story 1.3.1**: As a Data Engineer, I want cleaned Silver tables so Analysts and Scientists can trust the data.  
  - **Learning Resources**: Medallion Architecture, Schema Evolution, Data Quality Framework
  - **Tasks**:  
    - Deduplicate data with business keys  
    - Standardize schemas (types, naming)  
    - Apply business rules (e.g., exclude negative quantities)  

### Feature 1.4: Business Marts (Sprint 3)
- **User Story 1.4.1**: As a Data Engineer, I want Gold marts for sales and customers so business dashboards are fast and reliable.  
  - **Learning Resources**: Medallion Architecture, Performance Optimization, PySpark DataFrame API
  - **Tasks**:  
    - Build `sales_daily` mart  
    - Build `category_perf` mart  
    - Build `customer_360` mart (with RFM base)  
    - Document marts with schemas + lineage  

---

## Epic 2 - Analytics & Business Intelligence
**Goal**: Provide dashboards and insights for executives and marketing.

### Feature 2.1: Quick KPIs on Raw/Bronze (Sprint 1)
- **User Story 2.1.1**: As a Data Analyst, I want KPIs on raw data so I can deliver a first look dashboard.  
  - **Learning Resources**: PySpark DataFrame API, Databricks Free Edition Guide, Performance Optimization
  - **Tasks**:  
    - Compute GMV, AOV, orders per customer  
    - Build 1-page dashboard (Databricks SQL/Plotly)  

### Feature 2.2: Raw vs Silver Dashboard (Sprint 2)
- **User Story 2.2.1**: As a Data Analyst, I want to compare KPIs across Raw and Silver so I can show the impact of data cleaning.  
  - **Learning Resources**: Data Quality Framework, Performance Optimization, PySpark DataFrame API
  - **Tasks**:  
    - Redo KPIs on Silver  
    - Dashboard comparing Raw vs Silver (GMV, AOV, return rates)  
    - Document differences  

### Feature 2.3: Executive Dashboard (Sprint 3)
- **User Story 2.3.1**: As an Executive, I want a dashboard with GMV, AOV, margin so I can see sales trends.  
  - **Learning Resources**: Performance Optimization, Data Quality Framework, PySpark DataFrame API
  - **Tasks**:  
    - Dashboard with daily/weekly sales  
    - Add geographic performance (country, region)  

### Feature 2.4: Customer Segmentation Dashboard (Sprint 4)
- **User Story 2.4.1**: As a Marketing Manager, I want to see segments and churn risk so I can design campaigns.  
  - **Learning Resources**: Fabric Lakehouse Overview, Power BI Service, Fabric Data Pipelines
  - **Tasks**:  
    - Customer 360 dashboard with RFM segments  
    - Integrate churn scores (from DS)  
    - Publish in Power BI (Fabric)  

---

## Epic 3 - Machine Learning & Predictive Analytics
**Goal**: Develop churn prediction and integrate with Customer 360.

### Feature 3.1: Exploratory Analysis (Sprint 1)
- **User Story 3.1.1**: As a Data Scientist, I want to explore data so I can identify churn patterns.  
  - **Learning Resources**: MLflow Tracking, PySpark DataFrame API, Data Quality Framework
  - **Tasks**:  
    - EDA on Bronze (distributions, missing values)  
    - Draft churn definition (e.g., no orders in 90 days)  

### Feature 3.2: Feature Engineering (Sprint 2)
- **User Story 3.2.1**: As a Data Scientist, I want to create features so churn models can be trained.  
  - **Learning Resources**: MLflow Tracking, Schema Evolution, Performance Optimization
  - **Tasks**:  
    - Compute RFM metrics  
    - Add basket size, diversity features  
    - Log feature set in MLflow  

### Feature 3.3: Baseline Model Training (Sprint 3)
- **User Story 3.3.1**: As a Data Scientist, I want a baseline churn model so I can evaluate predictive power.  
  - **Learning Resources**: MLflow Tracking, Medallion Architecture, Performance Optimization
  - **Tasks**:  
    - Train Logistic Regression & Random Forest  
    - Log runs in MLflow  
    - Compare models  

### Feature 3.4: Batch Scoring & MLOps-lite (Sprint 4)
- **User Story 3.4.1**: As a Data Scientist, I want churn scores integrated into Gold so Analysts can use them in dashboards.  
  - **Learning Resources**: MLflow Tracking, Databricks Free Edition Guide, Performance Optimization
  - **Tasks**:  
    - Batch scoring job → `customer_scores_gold`  
    - Register model + version in MLflow  
    - Join churn probabilities into Customer 360  

---

## Epic 4 - Platform Integration
**Goal**: Demonstrate Fabric integration and orchestration.

### Feature 4.1: Export Gold to Fabric (Sprint 4)
- **User Story 4.1.1**: As a Data Engineer, I want Gold exported to Fabric so Power BI can consume data.  
  - **Learning Resources**: Fabric Lakehouse Overview, Fabric Data Pipelines, Performance Optimization
  - **Tasks**:  
    - Export Gold as Parquet (release folder, versioned)  
    - Upload to Fabric Lakehouse (`dropzone`)  
    - Ingest into Delta tables with notebook  

### Feature 4.2: Power BI Reports (Sprint 4)
- **User Story 4.2.1**: As a Data Analyst, I want Power BI dashboards so executives can use them.  
  - **Learning Resources**: Power BI Service, Fabric Lakehouse Overview, Fabric Data Pipelines
  - **Tasks**:  
    - Build Executive & Customer Risk dashboards in Fabric  
    - Configure refresh  
    - Document semantic model  

---

## Sprint Planning Matrix (4.5 days per profile per sprint)

| Sprint | DE Focus | DA Focus | DS Focus |
|--------|----------|----------|----------|
| **Sprint 0 (0.5d)** | Workspace setup, foldering | Backlog refinement, KPI brainstorm | Hypothesis brainstorm (churn, CLV) |
| **Sprint 1 (4.5d)** | Bronze ingestion, quality checks | KPIs on Raw/Bronze, 1st dashboard | EDA, churn definition draft |
| **Sprint 2 (4.5d)** | Silver cleaning, schema | Raw vs Silver dashboard | Feature engineering, MLflow tracking |
| **Sprint 3 (4.5d)** | Gold marts, export prep | Executive dashboard, geo analysis | Baseline churn models (MLflow) |
| **Sprint 4 (4.5d)** | Export Gold to Fabric, orchestration | Customer segmentation dashboard + Power BI | Batch churn scoring, integrate into Gold |

---

### Cross-role handoff map (who produces what for whom)
- Sprint 1:
  - DE → DA: Bronze tables + profile docs; DA builds first KPIs on Raw/Bronze.
  - DS consumes Bronze for EDA; shares churn definition proposal back to DE/DA.
- Sprint 2:
  - DE → DA/DS: Silver tables with stable schema; DA redoes KPIs vs Raw; DS engineers features on Silver.
- Sprint 3:
  - DE → DA/DS: Gold marts (sales_daily, customer_360) + release dry-run; DS trains baseline models using Silver/Gold.
  - DA builds Executive dashboard; flags any semantic issues for DE to fix in Gold.
- Sprint 4:
  - DS → DE: Batch churn scores; DE joins into customer_360 Gold and publishes release drop (contract above).
  - Fabric Data Pipeline ingests release into Lakehouse; DA finalizes Power BI with churn segments.

## Optional Extensions
- **Advanced Analytics**: Neural Nets, Ensemble methods, A/B testing
- **Production Readiness**: CI/CD pipelines, model drift detection
- **Portfolio Development (Hire Me Level)**: Public repo polish, LinkedIn/Medium posts, storytelling dashboards

#### User Story 1.1.2: Data Quality Monitoring
**As a** Data Engineer  
**I want to** implement automated data quality checks  
**So that** data issues are caught early in the pipeline  

**Learning Resources**:
- Data Quality Framework - Validation strategies
- PySpark DataFrame API - Data transformation
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- Quality rules defined and implemented
- Automated alerts for data anomalies
- Quality metrics tracked over time

**Tasks**:
- **Task 1.1.2.1**: Define data quality rules and thresholds
  - *Platform*: Databricks notebooks with business rule documentation
  - *Key Concepts*: Data profiling, statistical validation
  - *Reference*: [Data Profiling Techniques](https://docs.databricks.com/exploratory-data-analysis/index.html)
- **Task 1.1.2.2**: Implement validation logic in notebooks
  - *Technology*: PySpark functions (isNull, regexp_extract, when/otherwise)
  - *Key Concepts*: Column validation, row-level checks
  - *Reference*: [PySpark SQL Functions](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/functions.html)
- **Task 1.1.2.3**: Create quality dashboard
  - *Technology*: Databricks display(), matplotlib/plotly
  - *Key Concepts*: Data visualization, monitoring dashboards
  - *Reference*: [Databricks Visualizations](https://docs.databricks.com/notebooks/visualizations/index.html)

### Feature 1.2: Data Transformation & Cleaning
**Feature Description**: Transform raw data into clean, standardized formats suitable for analytics and ML.

**Technical Implementation Overview**:
**Primary Operations**: Data deduplication, type casting, business rule application  
**Key Databricks Concepts**: DataFrame transformations, Delta Lake MERGE operations  
**Performance Considerations**: Partitioning strategies, Z-ordering for optimal queries  

#### User Story 1.2.1: Silver Layer Data Cleaning
**As a** Data Analyst  
**I want to** have clean, deduplicated data in Silver layer  
**So that** I can perform reliable analytics without data quality issues  

**Learning Resources**:
- Medallion Architecture - Bronze/Silver/Gold pattern
- PySpark DataFrame API - Data transformation
- Schema Evolution - Managing changing data
- Data Quality Framework - Validation strategies

**Acceptance Criteria**:
- Duplicate records removed
- Data types standardized
- Business rules applied consistently

**Tasks**:
- **Task 1.2.1.1**: Implement deduplication logic
  - *Technology*: PySpark dropDuplicates(), window functions with row_number()
  - *Key Concepts*: Business key identification, temporal deduplication
  - *Reference*: [DataFrame Deduplication](https://docs.databricks.com/optimizations/semi-structured.html)
- **Task 1.2.1.2**: Standardize data types and formats
  - *Technology*: PySpark cast(), to_date(), regexp_replace()
  - *Key Concepts*: Type safety, data standardization patterns
  - *Reference*: [Data Type Conversion](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- **Task 1.2.1.3**: Apply business transformation rules
  - *Technology*: PySpark when().otherwise(), user-defined functions (UDF)
  - *Key Concepts*: Business logic implementation, data enrichment
  - *Reference*: [PySpark UDF Guide](https://docs.databricks.com/udf/index.html)
- **Task 1.2.1.4**: Create Silver layer documentation
  - *Platform*: Databricks notebooks with markdown documentation
  - *Key Concepts*: Data lineage, transformation logic documentation
  - *Reference*: [Data Documentation Best Practices](https://docs.databricks.com/data-governance/index.html)

#### User Story 1.2.2: Schema Standardization
**As a** Data Engineer  
**I want to** enforce consistent schemas across tables  
**So that** downstream consumers have predictable data structures  

**Learning Resources**:
- Schema Evolution - Managing changing data
- Delta Lake Architecture - Modern data lake storage
- PySpark DataFrame API - Data transformation

**Acceptance Criteria**:
- Schema evolution handled gracefully
- Consistent naming conventions applied
- Foreign key relationships maintained

**Tasks**:
- **Task 1.2.2.1**: Define standard schema patterns
  - *Technology*: Databricks schema definitions, StructType/StructField
  - *Key Concepts*: Schema design patterns, data type consistency
  - *Reference*: [Delta Lake Schema Evolution](https://docs.databricks.com/delta/delta-batch.html#table-schema-evolution)
- **Task 1.2.2.2**: Implement schema enforcement logic
  - *Technology*: PySpark enforceSchema option, schema validation
  - *Key Concepts*: Schema validation, error handling
  - *Reference*: [Schema Enforcement](https://docs.databricks.com/delta/delta-batch.html#schema-enforcement)
- **Task 1.2.2.3**: Create schema documentation
  - *Platform*: Databricks notebooks with schema registry documentation
  - *Key Concepts*: Metadata management, schema versioning
  - *Reference*: [Unity Catalog Schema Management](https://docs.databricks.com/data-governance/unity-catalog/index.html)

### Feature 1.3: Business Data Marts
**Feature Description**: Create business-focused data marts optimized for analytics and reporting.

**Technical Implementation Overview**:
**Primary Operations**: Aggregation, windowing functions, complex joins  
**Key Databricks Concepts**: Gold layer optimization, Z-ordering, liquid clustering  
**Performance Strategy**: Pre-aggregated metrics, partitioning by date/region  

#### User Story 1.3.1: Sales Analytics Mart
**As a** Business Analyst  
**I want to** have a sales analytics mart with daily aggregations  
**So that** I can quickly analyze sales performance and trends  

**Learning Resources**:
- Medallion Architecture - Bronze/Silver/Gold pattern
- Performance Optimization - Query tuning techniques
- PySpark DataFrame API - Data transformation

**Acceptance Criteria**:
- Daily sales metrics calculated and stored
- Historical data preserved
- Performance optimized for querying

**Tasks**:
- **Task 1.3.1.1**: Design sales mart schema
  - *Technology*: Star schema design, dimensional modeling principles
  - *Key Concepts*: Fact/dimension tables, business metrics definition
  - *Reference*: [Data Modeling Best Practices](https://docs.databricks.com/lakehouse/data-modeling.html)
- **Task 1.3.1.2**: Implement daily aggregation logic
  - *Technology*: PySpark groupBy(), window functions, date_trunc()
  - *Key Concepts*: Time-based aggregation, running totals, period-over-period
  - *Reference*: [Window Functions Guide](https://docs.databricks.com/sql/language-manual/functions/window.html)
- **Task 1.3.1.3**: Create performance indexes
  - *Technology*: Delta Lake Z-order optimization, partitioning strategies
  - *Key Concepts*: Query performance optimization, data skipping
  - *Reference*: [Delta Lake Performance Tuning](https://docs.databricks.com/optimizations/delta-cache.html)
- **Task 1.3.1.4**: Load historical data
  - *Technology*: Delta Lake time travel, MERGE operations
  - *Key Concepts*: Historical data preservation, incremental loading
  - *Reference*: [Delta Lake Time Travel](https://docs.databricks.com/delta/delta-batch.html#deltatimetravel)

#### User Story 1.3.2: Customer 360 Mart
**As a** Marketing Analyst  
**I want to** have a unified customer view with behavioral metrics  
**So that** I can segment customers and personalize marketing campaigns  

**Learning Resources**:
- PySpark DataFrame API - Data transformation
- Performance Optimization - Query tuning techniques
- Data Quality Framework - Validation strategies

**Acceptance Criteria**:
- RFM metrics calculated for all customers
- Customer lifecycle stages identified
- Behavioral features engineered

**Tasks**:
- **Task 1.3.2.1**: Calculate RFM metrics
  - *Technology*: PySpark window functions, percentile calculations, ntile()
  - *Key Concepts*: Recency/Frequency/Monetary analysis, customer scoring
  - *Reference*: [Customer Analytics with Spark](https://docs.databricks.com/scenarios/retail.html)
- **Task 1.3.2.2**: Define customer lifecycle stages
  - *Technology*: PySpark case/when logic, date arithmetic functions
  - *Key Concepts*: Customer journey mapping, lifecycle segmentation
  - *Reference*: [Advanced Analytics Patterns](https://docs.databricks.com/machine-learning/feature-store/index.html)
- **Task 1.3.2.3**: Engineer behavioral features
  - *Technology*: PySpark feature engineering, lag/lead functions
  - *Key Concepts*: Feature engineering for ML, behavioral patterns
  - *Reference*: [Feature Engineering Guide](https://docs.databricks.com/machine-learning/feature-engineering/index.html)
- **Task 1.3.2.4**: Create customer segmentation logic
  - *Technology*: PySpark MLlib clustering (KMeans), statistical functions
  - *Key Concepts*: Unsupervised learning, customer clustering
  - *Reference*: [MLlib Clustering](https://spark.apache.org/docs/latest/ml-clustering.html)

---

## Epic 2: Analytics & Business Intelligence
**Epic Goal**: Provide business stakeholders with interactive dashboards and insights to drive data-driven decisions.

### Feature 2.1: Executive Dashboards
**Feature Description**: High-level KPI dashboards for executive decision-making.

#### User Story 2.1.1: Executive KPI Dashboard
**As an** Executive  
**I want to** see key business metrics in real-time  
**So that** I can make informed strategic decisions  

**Learning Resources**:
- PySpark DataFrame API - Data transformation
- Databricks Free Edition Guide - Setup and limitations
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- GMV, AOV, customer acquisition metrics displayed
- Trends and comparisons visualized
- Mobile-responsive design

**Tasks**:
- **Task 2.1.1.1**: Define executive KPIs
- **Task 2.1.1.2**: Create interactive Plotly dashboard
- **Task 2.1.1.3**: Implement real-time data refresh
- **Task 2.1.1.4**: Design mobile-responsive layout

#### User Story 2.1.2: Geographic Performance Analysis
**As a** Regional Manager  
**I want to** analyze performance by country and region  
**So that** I can optimize regional strategies  

**Learning Resources**:
- PySpark DataFrame API - Data transformation
- Performance Optimization - Query tuning techniques
- Data Quality Framework - Validation strategies

**Acceptance Criteria**:
- Geographic breakdown of all major metrics
- Comparative analysis capabilities
- Drill-down functionality

**Tasks**:
- **Task 2.1.2.1**: Create geographic aggregations
- **Task 2.1.2.2**: Build interactive maps
- **Task 2.1.2.3**: Implement drill-down navigation

### Feature 2.2: Customer Analytics
**Feature Description**: Deep customer behavior analysis and segmentation tools.

#### User Story 2.2.1: Customer Segmentation Dashboard
**As a** Marketing Manager  
**I want to** understand customer segments and their behaviors  
**So that** I can design targeted marketing campaigns  

**Learning Resources**:
- PySpark DataFrame API - Data transformation
- Data Quality Framework - Validation strategies
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- RFM segmentation visualized
- Segment characteristics documented
- Cohort analysis included

**Tasks**:
- **Task 2.2.1.1**: Create RFM visualization
- **Task 2.2.1.2**: Build cohort retention charts
- **Task 2.2.1.3**: Analyze segment characteristics
- **Task 2.2.1.4**: Create segment comparison tools

### Feature 2.3: Power BI Integration
**Feature Description**: Export insights to Power BI for enterprise reporting.

#### User Story 2.3.1: Executive Reports in Power BI
**As a** Business Stakeholder  
**I want to** access reports through familiar Power BI interface  
**So that** I can share insights across the organization  

**Learning Resources**:
- Fabric Lakehouse Overview - Data storage concepts
- Power BI Service - Dashboard creation
- Fabric Data Pipelines - Data orchestration

**Acceptance Criteria**:
- Key dashboards replicated in Power BI
- Automated data refresh configured
- Sharing permissions set up

**Tasks**:
- **Task 2.3.1.1**: Export data to Fabric Lakehouse
- **Task 2.3.1.2**: Create Power BI reports
- **Task 2.3.1.3**: Configure automated refresh
- **Task 2.3.1.4**: Set up sharing and permissions

---

## Epic 3: Machine Learning & Predictive Analytics
**Epic Goal**: Develop predictive models to identify at-risk customers and optimize business outcomes.

### Feature 3.1: Customer Churn Prediction
**Feature Description**: ML models to predict customer churn and enable proactive retention.

#### User Story 3.1.1: Churn Model Development
**As a** Data Scientist  
**I want to** build accurate churn prediction models  
**So that** the business can proactively retain valuable customers  

**Learning Resources**:
- MLflow Tracking - ML experiment management
- PySpark DataFrame API - Data transformation
- Data Quality Framework - Validation strategies

**Acceptance Criteria**:
- Multiple models trained and compared
- Model performance documented
- Feature importance analyzed

**Tasks**:
- **Task 3.1.1.1**: Define churn target variable
- **Task 3.1.1.2**: Engineer predictive features
- **Task 3.1.1.3**: Train and validate models
- **Task 3.1.1.4**: Select best performing model

#### User Story 3.1.2: Churn Risk Scoring
**As a** Customer Success Manager  
**I want to** see churn risk scores for all customers  
**So that** I can prioritize retention efforts  

**Learning Resources**:
- MLflow Tracking - ML experiment management
- Medallion Architecture - Bronze/Silver/Gold pattern
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- Risk scores calculated for all active customers
- Scores integrated into customer dashboard
- Risk thresholds defined

**Tasks**:
- **Task 3.1.2.1**: Implement batch scoring pipeline
- **Task 3.1.2.2**: Integrate scores into Gold layer
- **Task 3.1.2.3**: Create risk threshold alerts
- **Task 3.1.2.4**: Build risk score dashboard

### Feature 3.2: Model Operations (MLOps)
**Feature Description**: Robust model lifecycle management and monitoring.

#### User Story 3.2.1: Model Tracking & Versioning
**As a** Data Scientist  
**I want to** track all model experiments and versions  
**So that** I can reproduce results and compare model performance  

**Learning Resources**:
- MLflow Tracking - ML experiment management
- Databricks Free Edition Guide - Setup and limitations
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- All models logged in MLflow
- Experiment metadata tracked
- Model versions managed

**Tasks**:
- **Task 3.2.1.1**: Set up MLflow tracking
- **Task 3.2.1.2**: Implement experiment logging
- **Task 3.2.1.3**: Create model comparison dashboard

---

## Epic 4: Platform Integration & Deployment
**Epic Goal**: Seamlessly integrate Databricks and Microsoft Fabric for enterprise-grade data platform.

### Feature 4.1: Cross-Platform Data Pipeline
**Feature Description**: Automated data flow between Databricks and Microsoft Fabric.

#### User Story 4.1.1: Automated Data Export
**As a** Data Engineer  
**I want to** automatically sync processed data to Fabric Lakehouse  
**So that** Power BI can access the latest data  

**Learning Resources**:
- Fabric Lakehouse Overview - Data storage concepts
- Fabric Data Pipelines - Data orchestration
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- Automated export process configured
- Data integrity validated
- Export schedule optimized

**Tasks**:
- **Task 4.1.1.1**: Design export pipeline architecture
- **Task 4.1.1.2**: Implement Parquet export logic
- **Task 4.1.1.3**: Configure automated scheduling
- **Task 4.1.1.4**: Add data validation checks

### Feature 4.2: Workflow Orchestration
**Feature Description**: Primary orchestration in Fabric Data Pipelines. Optional: demonstrate Apache Airflow for file-level validations/moves. Note: Databricks Free Edition cannot run Jobs/API-triggered runs reliably, so Airflow must not depend on Databricks Jobs on Free.

#### User Story 4.2.1: Workflow Orchestration with Airflow
**As a** Data Engineer  
**I want to** orchestrate and schedule data pipelines using Apache Airflow  
**So that** data processing runs automatically without manual intervention  

**Learning Resources**:
- Apache Airflow - Workflow orchestration and scheduling
- Airflow with Databricks - Integration patterns
- Performance Optimization - Query tuning techniques

**Acceptance Criteria**:
- Fabric Data Pipeline orchestrates release ingestion end-to-end
- Optional Airflow DAG demonstrates external file checks/moves independent of Databricks Jobs
- Error handling and retry logic implemented
- Pipeline monitoring and alerting configured

**Tasks**:
- **Task 4.2.1.1**: Set up Apache Airflow environment
  - *Technology*: Docker Compose, Airflow installation
  - *Key Concepts*: DAG definition, task dependencies
  - *Reference*: [Airflow Quick Start](https://airflow.apache.org/docs/apache-airflow/stable/start.html)
- **Task 4.2.1.2**: Create DAGs for file-level validation and staging
  - *Technology*: Sensors on release/_SUCCESS, checksum validation, file moves (landing → validated)
  - *Key Concepts*: Data readiness checks, atomic publish, decoupled orchestration
  - *Reference*: [Airflow Sensors](https://airflow.apache.org/docs/apache-airflow/stable/concepts/sensors.html)
- **Task 4.2.1.3**: Implement error handling and monitoring
  - *Technology*: Airflow sensors, email notifications, Slack webhooks
  - *Key Concepts*: Pipeline reliability, failure recovery
  - *Reference*: [Airflow Error Handling](https://airflow.apache.org/docs/apache-airflow/stable/concepts/dags.html#task-failure)
- **Task 4.2.1.4**: Configure pipeline scheduling and dependencies
  - *Technology*: Cron expressions, task dependencies, sensors
  - *Key Concepts*: Automated scheduling, data freshness validation
  - *Reference*: [Airflow Scheduling](https://airflow.apache.org/docs/apache-airflow/stable/concepts/scheduler.html)

---

## Sprint Planning Matrix

| Sprint | Duration | Epic Focus | Key Deliverables |
|--------|----------|------------|------------------|
| **Sprint 0** | 0.5 days | Setup | Environment setup, team formation, backlog refinement |
| **Sprint 1** | 4.5 days | Epic 1 (Foundation) | Bronze layer, initial data ingestion |
| **Sprint 2** | 4.5 days | Epic 1 (Transformation) | Silver layer, data cleaning |
| **Sprint 3** | 4.5 days | Epic 1 & 2 (Marts & Analytics) | Gold layer, initial dashboards |
| **Sprint 4** | 4.5 days | Epic 2, 3 & 4 (ML & Integration) | ML models, Power BI integration, Airflow orchestration |

---

## Advanced Level Options (Optional Extensions)

For teams seeking additional challenges, the following advanced epics can be added:

### Epic 5: Advanced Analytics (Stretching Level)
- Real-time data processing
- Advanced ML models (Neural Networks, Ensemble methods)
- A/B testing frameworks

### Epic 6: Production Readiness (Excellence Level)  
- Automated CI/CD pipelines
- Model monitoring and drift detection
- Enterprise security implementation

### Epic 7: Portfolio Development (Hire Me Level)
- Professional documentation
- Public repository showcase
- Technical blog posts and presentations

---

*This backlog follows Agile best practices and can be imported into Azure DevOps, Jira, or other project management tools for team collaboration.*










---

---

### Power BI → Databricks on Free Edition (S1–S2): connection steps
1) In Databricks Free: start cluster; create Personal Access Token.  
2) Capture Server Hostname and cluster HTTP Path (JDBC/ODBC settings).  
3) Ensure tables/views exist in `hive_metastore` (default schema); create externals over Bronze/Silver if needed.  
4) In Power BI Desktop: Get Data → Databricks; mode = DirectQuery (or Import for small demos).  
5) Auth = Personal Access Token; paste Hostname + HTTP Path; pick tables; build visuals.

Constraints  
- Cluster auto-stops after ~2h idle; DirectQuery fails until restarted.  
- Performance is limited; prefer pre-aggregation/views in Databricks; avoid heavy joins from Power BI.


+ as bien : Integration with Fabric
Free path: Export Gold as versioned Parquet + manifest; upload/copy into Fabric Lakehouse; ingest to Delta tables via Fabric Data Pipelines.
Paid path: Use ADLS Gen2 + Shortcuts to point Fabric Lakehouse to Gold folders; or pull via Pipelines; schedule end-to-end.
Build a Composite Power BI model exposing both datasets through a single semantic layer.
Publish consolidated dashboards as the "Post-Merger Analytics Suite". + integration de databricks avec fabric - explqiuer  à mieux fiare 
