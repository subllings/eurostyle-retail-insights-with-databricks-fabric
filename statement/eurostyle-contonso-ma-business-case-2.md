# EuroStyle–Contoso M&A – Insights with Databricks & Fabric

## Business Case

EuroStyle is a European online fashion retailer operating in the UK, France, Germany, and the Benelux.  
In early 2025, EuroStyle acquired **Contoso Retail**, a Spanish and Italian fashion chain with a strong customer base in Southern Europe.

Together, the two brands now serve **over 350,000 customers** and manage a combined portfolio of more than **80,000 SKUs** across apparel, footwear, and accessories.

The merger brings opportunities but also challenges:
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
- **AOV (Average Order Value)**: Harmonized calculation based on a unified order table.  
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


## Sprint Structure (High-Level)

| Sprint | Duration | Focus (M&A Context) | Key Deliverables | Platforms |
|--------|----------|----------------------|------------------|-----------|
| **Sprint 0** | 0.5 day | Setup & Planning | Workspace setup, schema documentation, backlog refinement, KPI catalog | Databricks (workspace) · Fabric (semantic model draft) |
| **Sprint 1** | 4.5 days | Bronze & First Insights | Bronze ingestion (EuroStyle + Contoso), Raw KPIs dashboard | Databricks (Bronze tables) · Power BI (DirectQuery dashboard) |
| **Sprint 2** | 4.5 days | Silver & Harmonization | Cleaned Silver, schema alignment, Raw vs Silver dashboard | Databricks (Silver tables) · Power BI (Silver dashboard) |
| **Sprint 3** | 4.5 days | Gold & Post-Merger Views | Unified Gold marts, executive consolidated dashboard | Databricks (Gold marts) · Fabric/Power BI (exec dashboards) |
| **Sprint 4** | 4.5 days | MLflow & Fabric Integration | Churn/CLV scoring, export to Fabric, Power BI Post-Merger Suite | Databricks (MLflow models, scored tables) · Fabric (Lakehouse, Direct Lake) · Power BI (Post-Merger Suite) |

**Note – Sprint 1 (Bronze in Databricks):**  
You expose your Bronze tables via the `hive_metastore` (or SQL views).  
In Power BI Desktop: *Get Data → Databricks* → provide **Server Hostname** and **HTTP Path** (found in cluster JDBC/ODBC settings).

---


## Roles per Sprint (EuroStyle–Contoso M&A)



| Sprint | Data Engineer (DE) | Data Scientist (DS) | Data Analyst (DA) | Platforms |
|---|---|---|---|---|
| **0** | Set up Databricks workspace & folders; define ingestion paths for EuroStyle + Contoso | Define churn/CLV hypotheses, identify data requirements, prepare MLflow experiments | Document KPI catalog (EuroStyle vs Contoso), map differences, design draft semantic model | DE: Databricks · DS: Databricks (MLflow) · DA: Fabric/Power BI |
| **1** | Ingest EuroStyle + Contoso raw CSVs into **Bronze Delta**; add metadata & data quality checks | EDA on Bronze (distributions, missing values, overlap) | Build "First Look Dashboard" with Raw/Bronze KPIs (GMV, AOV, order counts) | DE: Databricks (Bronze) · DS: Databricks · DA: Power BI (DirectQuery to Databricks SQL) |
| **2** | Transform Bronze → Silver (deduplication, schema harmonization, currency alignment) | Feature engineering (RFM, overlap, basket diversity) with MLflow tracking | Redesign dashboard with Silver data; show Raw vs Silver KPIs; define RLS/governance | DE: Databricks (Silver) · DS: Feature store/MLflow · DA: Power BI |
| **3** | Create Gold marts (`sales_daily`, `category_perf`, unified `customer_360`) | Train baseline churn/CLV models (LR, RF); log runs in MLflow | Executive Post-Merger Dashboard (consolidated view, brand/geography splits) | DE: Databricks (Gold) · DS: MLflow · DA: Fabric/Power BI (Gold marts) |
| **4** | Export Gold marts → Fabric Lakehouse (Shortcuts/Parquet); orchestrate | Batch scoring of churn/CLV; publish scores into Gold; register models | Unified Power BI dashboards with RLS, Customer Segmentation + Churn, deployment pipelines | DE: Databricks + Fabric · DS: Databricks + Fabric · DA: Power BI/Fabric |


---

### Platform Context

This prototype is built using **Databricks Free Edition** together with **Microsoft Fabric Free/Trial**.  
While both free tiers impose limitations, they are sufficient to demonstrate the full integration of Databricks (for Medallion architecture and MLflow) and Fabric (for Lakehouse, Direct Lake, and Power BI dashboards).

---

### Constraints vs. Viability

| Aspect              | Free Tier Constraints                                        | How the Solution Still Works |
|---------------------|--------------------------------------------------------------|-------------------------------|
| **Databricks cluster** | Auto-stops after ~2h, limited performance                 | Acceptable for Bronze/Silver exploration; clusters can be restarted when needed |
| **Storage**         | No ADLS Gen2 integration in Free Edition                     | Data exported as Parquet + manifest; uploaded manually to Fabric Lakehouse |
| **Fabric capacity** | Limited to trial capacity; not production-scale              | Enough to validate Direct Lake, semantic models, and Power BI dashboards |
| **Orchestration**   | Manual triggers, limited scheduling                          | Pipelines in Fabric ensure ingestion and refresh; manual Databricks export is acceptable for demo |
| **Security & RBAC** | No enterprise-level governance in free tiers                 | Role-based access simulated at dashboard level (Power BI RLS) |
| **BI mode**         | DirectQuery on Databricks cluster in Sprint 1–Sprint 2; Direct Lake only later | Demonstrates end-to-end journey from raw Bronze (DirectQuery) to governed Fabric (Direct Lake) |

---

In other words, although the free editions lack enterprise features (ADLS integration, automation, large-scale performance), they allow us to deliver a **working prototype** of a unified EuroStyle–Contoso post-merger platform, showcasing ingestion, harmonization, analytics, and predictive modeling across Databricks and Fabric.




---

## Agile Way of Working

- **Sprint Planning**: Select must-have and stretch tasks.  
- **Daily Stand-up**: Share progress, blockers, next steps.  
- **Sprint Review**: Demo dashboards, Delta tables, MLflow runs.  
- **Sprint Retrospective**: Reflect on improvements.

---



## Deliverables Overview

- **Must-Have**: Dual Bronze ingestion, Raw/Bronze side-by-side dashboards.  
- **Stretching**: Unified Silver schema, harmonized Gold marts, consistent churn logic.  
- **Excellence**: Churn/CLV models, Fabric integration, executive dashboards.  
- **Hire Me Level**: Portfolio storytelling — *"Post-Merger Analytics with Databricks & Fabric"*.

---

## Dataset

You will work with **two European retail datasets** to simulate EuroStyle (North/West Europe) and Contoso (South Europe).

**EuroStyle – Primary Dataset (Online Retail II, UCI)**  
- Link: https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci  
- Notes: Real transactional data (invoice-level), single-table CSV. Good for Bronze ingestion and baseline KPIs.

**Contoso – Secondary Dataset (European Fashion Store – Multitable)**  
- Link: https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset  
- Notes: Synthetic but realistic, multiple related tables (orders, products, customers). Ideal for schema harmonization and joins.

**Upload instructions (Databricks Free Edition)**  
- Download CSVs locally, then upload to:  
  - EuroStyle: `/FileStore/retail/raw/eurostyle/...`  
  - Contoso: `/FileStore/retail/raw/contoso/...`

**Example column mapping (to harmonize schemas)**

| Unified field        | EuroStyle (Online Retail II) | Contoso (European Fashion Store) |
|---|---|---|
| `order_id`           | `InvoiceNo`                   | `order_id` |
| `order_date`         | `InvoiceDate`                 | `order_date` |
| `customer_id`        | `CustomerID`                  | `customer_id` |
| `sku`                | `StockCode`                   | `sku` or `product_id` |
| `product_name`       | `Description`                 | `product_name` |
| `quantity`           | `Quantity`                    | `quantity` |
| `unit_price`         | `UnitPrice`                   | `unit_price` |
| `country`            | `Country`                     | `country` |
| `currency`           | inferred/assumed              | `currency` (if present) |

Recommendations  
- Normalize currencies to a common target (e.g., EUR) before Silver.  
- Build mapping tables for `sku` and product hierarchy.  
- Deduplicate by `(order_id, sku, customer_id, order_date)` where needed.  
- Add `source_system` column: `eurostyle` or `contoso`.

---

## Detailed Deliverables

For full details of tasks, acceptance criteria, and technical specifications, see the **[Project Backlog](../statement/3-eurostyle-project-backlog.md)** (adapted to M&A).

---
