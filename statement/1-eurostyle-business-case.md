# EuroStyle Retail – Insights with Databricks & Fabric

## Business Case

EuroStyle is a European online fashion retailer operating in the UK, France, Germany, and the Benelux.  
The company has grown steadily over the last decade, reaching more than **200.000 customers** and offering **over 50.000 SKUs** (SKU represents a distinct product and variation) in apparel, footwear, and accessories.  

Despite its growth, EuroStyle is facing several challenges:
- **Eroding margins** caused by high return rates, particularly in Germany and the Benelux.  
- **Fragmented data sources** stored across different files and systems, preventing a unified customer view.  
- **Lack of reliable KPIs**: executives struggle to track Gross Merchandise Value (GMV), Average Order Value (AOV), and profit margins with inconsistent Excel-based reporting.  
- **Customer churn risk** suspected but not measured or predicted.  
- **Slow decision-making**: business questions take days to answer because analysts manually combine multiple files.  

The **Chief Marketing Officer (CMO)** has tasked three specialized teams to deliver a prototype of a **modern data platform** that provides both **business insights** and **predictive capabilities**:

- **Data Engineering Team**: Build robust data pipelines and infrastructure
- **Data Analytics Team**: Create business intelligence and visualization solutions  
- **Data Science Team**: Develop machine learning models for predictive analytics

---

## Key Business Metrics Definitions

- **GMV (Gross Merchandise Value)**: Total value of all orders placed, regardless of returns or cancellations
- **AOV (Average Order Value)**: Average amount spent per transaction (GMV ÷ Number of Orders)  
- **SKU (Stock Keeping Unit)**: Distinct product and variation (e.g., "Blue T-shirt Size M")
- **RFM Analysis**: Customer segmentation based on Recency, Frequency, and Monetary value
- **Customer 360°**: Unified view combining transaction history, demographics, and behavioral data
- **Churn**: Customers who haven't made a purchase within a defined period (e.g., 90 days)

---

## Technical Constraints & Considerations

### Databricks Free Edition Limitations
- **Compute Resources**: Limited to 1 driver + 1 worker node (max 15GB RAM total)
- **No Automated Jobs**: Manual notebook execution only, no scheduling capabilities
- **MLflow Limitations**: Model tracking available, but no model serving endpoints
- **Cluster Timeout**: Automatic shutdown after 2 hours of inactivity
- **Storage**: 15GB limit for DBFS (Databricks File System)

### Project Adaptations
- **Model Selection**: Focus on lightweight algorithms that run efficiently on limited resources
- **Data Size**: Work with sample datasets or data subsets to respect memory constraints
- **Manual Orchestration**: Use notebooks with clear execution order instead of automated pipelines
- **Local Model Serving**: Deploy models locally or on free cloud services instead of Databricks serving

### Microsoft Fabric Considerations
- **Free Trial**: 60-day trial period available for students
- **Power BI**: Free version has sharing limitations, suitable for individual development
- **Data Transfer**: Manual export/import process between Databricks and Fabric

---

## Project Objectives

1. **Data Engineering**:  
   - Build a reproducible **Medallion architecture** (Bronze → Silver → Gold) in Databricks Free Edition (https://www.databricks.com/learn/free-edition).  
   - Ensure **data quality, reliability, and versioning** using Delta Lake.  

2. **Data Analytics**:  
   - Provide executives with **dashboards** that show sales performance: total revenue (GMV), customer spending patterns (AOV), profit margins, and return rates.  
   - Create a **Customer 360° view** linking transactions, demographics, and engagement.  

3. **Data Science**:  
   - Engineer features such as **RFM (Recency, Frequency, Monetary)** and product diversity.  
   - Develop **machine learning models** to predict churn and estimate customer value.  
   - Track all experiments with **MLflow** and publish churn scores back into the Gold layer.  

4. **Integration with Fabric**:  
   - Simulate enterprise consumption of insights by **exporting Gold tables from Databricks Free Edition** to Microsoft Fabric.  
   - Ingest the data into a **Fabric Lakehouse** and expose it in **Power BI dashboards**.  
   - Experience real-world data platform architecture: Databricks for advanced analytics and ML, Fabric for enterprise data sharing and business intelligence. This hybrid approach allows data teams to leverage Databricks' powerful compute for complex transformations while providing business users with familiar Microsoft tools for reporting and decision-making.  

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

> Collaboration is mandatory:  
>- Analysts raise questions → Engineers implement data cleaning rules.  
>- Engineers provide cleaned Silver data → Scientists use it for feature engineering.  
>- Scientists provide churn predictions → Analysts visualize them for business decision-making.  



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



## Deliverables Overview

### Must-Have (Foundation for Success)
Core requirements for all teams to deliver a functional data platform prototype.

### Stretching (Ambitious)
Advanced features that demonstrate technical depth and business value.

### Excellence (Mastery)
Professional-grade implementations ready for production environments.

### Hire Me Level (Portfolio & Personal Branding)
Career-focused deliverables that differentiate candidates in the job market.

*Detailed task breakdown and platform specifications are available in the Learning Methodology guide.*

---

## Dataset  

You will work with **European retail datasets from Kaggle**:

**Primary Dataset (Recommended):**
- **Online Retail II (UCI – UK Retailer)**  
  [https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)  
  *Real transactional data, single table format, perfect for learning Medallion architecture basics*

**Alternative Dataset (Advanced Challenge):**
- **European Fashion Store – Multitable Dataset**  
  [https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset](https://www.kaggle.com/datasets/joycemara/european-fashion-store-multitable-dataset)  
  *Synthetic but realistic, multiple related tables, requires more complex joins and data modeling*

**Choose based on your team's comfort level:**
- **Online Retail II**: Start here if you want to focus on pipeline architecture and ML
- **European Fashion Store**: Choose this if you want additional complexity in data engineering (table relationships, foreign keys, normalization)  

Students must download the CSV files and upload them into Databricks Free Edition under `/FileStore/retail/raw/...`.

---

## Detailed Deliverables

For complete task breakdown with acceptance criteria and technical specifications, see the **[Project Backlog](./3-eurostyle-project-backlog.md)**.

The project is structured in 4 progressive levels:
- **Must-Have**: Core 6 tasks per profile (foundation for success)
- **Stretching**: Advanced features demonstrating technical depth
- **Excellence**: Professional-grade, production-ready implementations
- **Hire Me Level**: Portfolio and personal branding deliverables

Each level builds upon the previous one, allowing teams to demonstrate progressively advanced skills while maintaining a functional deliverable at each stage.

---
