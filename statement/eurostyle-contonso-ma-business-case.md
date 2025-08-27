# EuroStyle–Contoso M&A – Insights with Databricks & Fabric

## Business Case

EuroStyle is a European online fashion retailer operating in the UK, France, Germany, and the Benelux.  
In early 2025, EuroStyle acquired **Contoso Retail**, a Spanish and Italian fashion chain with a strong customer base in Southern Europe.  

Together, the two brands now serve **over 350,000 customers** and manage a combined portfolio of more than **80,000 SKUs** across apparel, footwear, and accessories.  

The **merger brings opportunities but also challenges**:

- **Data fragmentation**: EuroStyle and Contoso rely on separate systems (files, ERPs, CRMs). Consolidating them into a single analytical view is essential.  
- **Inconsistent KPIs**: GMV, AOV, margin, and return rates are calculated differently across the two companies, leading to confusion at board level.  
- **Post-merger visibility gap**: Executives need to compare EuroStyle vs. Contoso performance but also see a **unified sales and customer view**.  
- **Churn risk & cannibalization**: Overlapping product lines and customer bases may increase churn.  
- **Slow integration process**: Without a modern data platform, analysts spend days manually stitching EuroStyle and Contoso datasets.  

The **Chief Marketing Officer (CMO)** and **Chief Data & Analytics Officer (CDAO)** have tasked three specialized teams to deliver a **prototype of a unified post-merger data platform** leveraging Databricks and Microsoft Fabric:

- **Data Engineering Team**: Build robust pipelines to integrate EuroStyle and Contoso sources into a harmonized Medallion architecture.  
- **Data Analytics Team**: Provide executives with comparative and consolidated dashboards.  
- **Data Science Team**: Develop predictive models to assess churn and customer lifetime value across the merged customer base.  

---

## Key Business Metrics Definitions

- **GMV (Gross Merchandise Value)**: Unified definition across EuroStyle & Contoso to ensure consistent reporting.  
- **AOV (Average Order Value)**: Harmonized calculation based on unified order table.  
- **SKU (Stock Keeping Unit)**: Alignment of product hierarchies between EuroStyle and Contoso (mapping tables).  
- **RFM Analysis**: Post-merger segmentation showing differences in behavior between Northern and Southern European customers.  
- **Customer 360°**: Unified customer entity across both companies, with deduplication and cross-brand identifiers.  
- **Churn**: Customers inactive for >90 days, measured consistently across both brands.  


---

## Project Objectives

### Data Engineering
- Build a reproducible **Medallion pipeline** that ingests EuroStyle and Contoso raw data into Bronze, cleans into Silver, and harmonizes into Gold.  
- Align schemas (products, customers, orders) and manage mapping tables for cross-brand consistency.  

### Data Analytics
- Deliver dashboards showing:  
  - **EuroStyle vs Contoso performance side-by-side**.  
  - **Unified post-merger view** (consolidated GMV, AOV, margins).  
- Implement **role-based access (RLS)** so managers see only their brand, executives see both.  

### Data Science
- Develop **churn and CLV models** across merged data.  
- Investigate **customer overlap** (multi-brand shoppers).  
- Predict **cross-selling opportunities** (EuroStyle customers buying Contoso products, and vice versa).  

### Integration with Fabric
- Free path: Export Gold as versioned Parquet + manifest; Upload/Copy into Fabric Lakehouse; ingest to Delta tables via Fabric Data Pipelines.  
- Paid path: Use **ADLS Gen2 + Shortcuts** to point Fabric Lakehouse to Gold folders; or pull via Pipelines; schedule end-to-end.  
- Build a **Composite Power BI model** exposing both datasets through a single semantic layer.  
- Publish consolidated dashboards as the **"Post-Merger Analytics Suite"**.  

---

## Profiles and Responsibilities

- **Data Engineers (DE)**  
  - Own ingestion for EuroStyle + Contoso.  
  - Harmonize schemas, clean data, integrate into unified Gold marts.  
  - Export unified Gold to Fabric Lakehouse.  

- **Data Analysts (DA)**  
  - Define KPIs from Day 0 (EuroStyle vs Contoso differences).  
  - Build side-by-side dashboards early (Bronze/Raw) and evolve them on Silver & Gold.  
  - Deliver consolidated executive dashboards in Power BI.  

- **Data Scientists (DS)**  
  - Define churn/CLV hypotheses from Sprint 0.  
  - Perform EDA, feature engineering, and model training on Silver/Gold.  
  - Publish churn & CLV scores into the Customer 360 mart.  

---

## Roles per Sprint (EuroStyle–Contoso M&A)

| Sprint | Data Engineer (DE) | Data Scientist (DS) | Data Analyst (DA) | Platforms |
|--------|--------------------|---------------------|-------------------|-----------|
| **Sprint 0** | Set up Databricks workspace & folders; define ingestion paths for EuroStyle + Contoso | Define churn/CLV hypotheses, identify data requirements, prepare MLflow experiments | **Document KPI catalog (EuroStyle vs Contoso), map differences, design draft semantic model** | DE: Databricks <br> DS: Databricks (MLflow setup) <br> DA: Fabric/Power BI |
| **Sprint 1** | Ingest EuroStyle + Contoso raw CSVs into **Bronze Delta**; add metadata & data quality checks | Perform **EDA on Bronze** (distributions, missing values, overlap between brands) | **Build "First Look Dashboard"** with Raw/Bronze KPIs (GMV, AOV, order counts) | DE: Databricks (Bronze) <br> DS: Databricks (EDA, MLflow) <br> DA: Fabric/Power BI (DirectQuery to Databricks SQL) |
| **Sprint 2** | Transform Bronze → Silver (deduplication, schema harmonization, currency alignment) | Feature engineering (RFM, overlap, basket diversity) with MLflow tracking | **Redesign dashboard with Silver data**; show Raw vs Silver KPIs; define RLS/governance | DE: Databricks (Silver) <br> DS: Databricks (feature store, MLflow) <br> DA: Power BI (Silver-based models) |
| **Sprint 3** | Create Gold marts (`sales_daily`, `category_perf`, unified `customer_360`) | Train baseline churn/CLV models (LR, RF); log runs in MLflow | **Executive Post-Merger Dashboard** (EuroStyle + Contoso consolidated view, brand/geography splits) | DE: Databricks (Gold) <br> DS: Databricks (MLflow) <br> DA: Fabric/Power BI (Gold marts) |
| **Sprint 4** | Export Gold marts → Fabric Lakehouse (via Shortcuts/Parquet); orchestrate with Airflow | Batch scoring of churn/CLV; publish scores into Gold; register models in MLflow | **Unified Power BI dashboards** with RLS, Customer Segmentation + Churn dashboards, deployment pipelines | DE: Databricks + Fabric <br> DS: Databricks + Fabric (scored tables) <br> DA: Power BI/Fabric |



---

## Agile Way of Working

The project follows Agile practices with short sprints:  

- **Sprint Planning**: Select must-have and stretch tasks.  
- **Daily Stand-up**: Share progress, blockers, next steps.  
- **Sprint Review**: Demo dashboards, Delta tables, MLflow runs.  
- **Sprint Retrospective**: Reflect on improvements.  

---

## Sprint Structure (High-Level)

| Sprint | Duration | Focus (M&A Context) | Key Deliverables |
|--------|----------|----------------------|-----------------|
| **Sprint 0** | 0.5 day | Setup & Planning | Workspace setup, schema documentation, backlog refinement, KPI catalog |
| **Sprint 1** | 4.5 days | Bronze & First Insights | Bronze ingestion (EuroStyle + Contoso), Raw KPIs dashboard |
| **Sprint 2** | 4.5 days | Silver & Harmonization | Cleaned Silver, schema alignment, Raw vs Silver dashboard |
| **Sprint 3** | 4.5 days | Gold & Post-Merger Views | Unified Gold marts, executive consolidated dashboard |
| **Sprint 4** | 4.5 days | MLflow & Fabric Integration | Churn/CLV scoring, export to Fabric, Power BI Post-Merger Suite |

---

## Deliverables Overview

- **Must-Have**: Dual Bronze ingestion, Raw/Bronze side-by-side dashboards.  
- **Stretching**: Unified Silver schema, harmonized Gold marts, consistent churn logic.  
- **Excellence**: Churn/CLV models, Fabric integration, executive dashboards.  
- **Hire Me Level**: Portfolio storytelling — *"Post-Merger Analytics with Databricks & Fabric"*.  

---

## Dataset

You will work with **European retail datasets from Kaggle** to simulate EuroStyle and Contoso:

**Primary Dataset (EuroStyle – Online Retail II):**  
- [Online Retail II (UCI – UK Retailer)](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)  
  *Real transactional data, single-table format, perfect for Medallion pipeline basics.*  

**Secondary Dataset (Contoso – European Fashion Store):**  
- [European Fashion Store – Multitable Dataset](https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset)  
  *Synthetic but realistic, multiple related tables, ideal to simulate schema harmonization challenges.*  

> These two datasets simulate two companies with different schemas, forcing **schema harmonization** and **data bridging** — the > core of the M&A challenge.  

Students must download the CSV files from Kaggle and upload them into **Databricks Free Edition** under:  
`/FileStore/retail/raw/...`


---

## Detailed Deliverables

For full details of tasks, acceptance criteria, and technical specifications, see the **[Project Backlog](../statement/3-eurostyle-project-backlog.md)** (adapted to M&A).  

---
