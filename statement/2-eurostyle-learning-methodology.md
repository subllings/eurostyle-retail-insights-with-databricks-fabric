# EuroStyle Project - Learning Methodology (Databricks & Fabric)

## Pedagogical Approach

This project follows a **modern learning methodology** combining multiple proven educational techniques for maximum effectiveness in technical skill development.

### 1. Just-in-Time Learning (JIT Learning)
- **Learn when you need it**: Theory is provided right before you apply it
- **Avoid cognitive overload**: No information dump, focused learning
- **Maximize retention**: Immediate application reinforces understanding

### 2. Microlearning
- **Small learning units**: Each lesson is 15-20 minutes maximum
- **Precise learning objectives**: Clear, measurable goals for each task
- **Concrete deliverable**: Every task produces a tangible result

### 3. Learning by Doing / Constructivism
- **Immediate practice**: Apply theory right after learning it
- **Build knowledge through experience**: Hands-on construction of understanding
- **Highly effective for technical skills**: Perfect for data engineering, analytics, and ML

### 4. Scaffolding (Educational Scaffolding)
- **Structured support that gradually decreases**: Strong guidance at first → progressive autonomy
- **Each task prepares the next**: Building complexity step by step
- **Safe learning environment**: Mistakes are learning opportunities

### 5. Problem-Based Learning (PBL)
- **Real-world problem solving**: Focus on concrete business challenges
- **Professional context**: EuroStyle case mirrors actual industry scenarios
- **Intrinsic motivation**: Relevance drives engagement

### 6. Agile Learning
- **Short sprints with rapid feedback**: Quick iterations and continuous improvement
- **Team collaboration**: Cross-functional learning and knowledge sharing
- **Adaptive approach**: Adjust based on progress and blockers

---

## How to Work with This Project

### **Don't do this:**
- Read all documentation first, then start coding
- Try to understand everything before beginning
- Work in isolation without team interaction

### **Do this instead:**
1. **Read the Business Case** (overview understanding)
2. **Start Sprint 0** (setup and team formation)
3. **Work task by task** following this structure:
   - **Read the theory** (just enough for the task)
   - **Apply immediately** (hands-on practice)
   - **Deliver the result** (concrete output)
   - **Share with team** (collaboration and feedback)

---

## Task-Based Learning Structure

Each sprint is broken down into **micro-tasks** with this pattern:

### Task Template
```
**Task X.Y: [Task Name]**
**Estimated Time**: 30-45 minutes
**Learning Objective**: [Specific skill/knowledge]

**Theory (15 min)**: 
- Key concept explanation
- Relevant documentation links
- Best practices

**Practice (25 min)**:
- Step-by-step instructions
- Code examples
- Troubleshooting tips

**Deliverable**:
- [Specific output expected]
- [Quality criteria]

**Team Collaboration**:
- [How to share/review with team]
```

---

## Task Organization & Project Structure

**For complete task breakdown with acceptance criteria and technical specifications, see the [Project Backlog](./3-eurostyle-project-backlog.md).**

This methodology guide focuses on the pedagogical approach and learning progression. All detailed tasks are organized in the dedicated backlog document which serves as the single source of truth for project management.

## Sprint Progression

### Learning Progression Framework

The project follows a **4-sprint progression** designed around the **Medallion Architecture** (Bronze → Silver → Gold), ensuring each team member develops both technical skills and business acumen:

#### Sprint 0: Foundation (0.5 day)
*Just-in-Time Learning*: Tool setup and project orientation
- Environment configuration across platforms
- Team formation and role clarification
- Technical constraints understanding

#### Sprint 1: Bronze Layer (4.5 days)  
*Learning by Doing*: Raw data ingestion and basic operations
- First contact with real retail data
- Delta Lake fundamentals and pipeline concepts
- Initial data exploration and quality assessment

#### Sprint 2: Silver Layer (4.5 days)
*Problem-Based Learning*: Data cleaning and business rule implementation
- Collaborative problem-solving on data quality issues
- Business logic translation to technical requirements
- Cross-team validation and iteration

#### Sprint 3: Gold Layer (4.5 days)
*Scaffolding*: Business marts and advanced analytics
- Technical skills application to business outcomes
- Dashboard creation and ML model development
- Integration between platforms and teams

#### Sprint 4: Integration & Deployment (4.5 days)
*Constructivism*: Real-world deployment challenges
- End-to-end pipeline validation
- Cross-platform data export and visualization
- Presentation of complete business solution

**For detailed task specifications, acceptance criteria, and technical requirements, see the [Project Backlog](./3-eurostyle-project-backlog.md).**

---

## Success Factors

### For Learners:
- **Embrace the "not knowing"**: It's normal not to understand everything at first
- **Focus on the current task**: Don't get overwhelmed by the big picture
- **Ask questions immediately**: Don't wait until you're stuck
- **Share your progress**: Regular team check-ins accelerate learning

### For Coaches:
- **Provide just enough theory**: Resist the urge to explain everything upfront
- **Celebrate small wins**: Every completed task is progress
- **Encourage experimentation**: Safe-to-fail environment promotes learning
- **Facilitate collaboration**: Cross-team learning multiplies knowledge

---

## Quality Assurance

Each task includes:
- **Clear acceptance criteria**: You know when you're done
- **Self-assessment questions**: Validate your understanding
- **Peer review opportunities**: Learn from each other
- **Coach checkpoints**: Expert feedback at key milestones

---

*This methodology ensures you build practical skills while understanding the business context, preparing you for real-world data projects.*

---

## Detailed Deliverables by Level

### Must-Have (Foundation for Success)
- **Data Engineers (DE)**
  - Build Bronze → Silver → Gold pipeline in Databricks Free Edition using manual notebooks.
  - Bronze: raw CSV ingestion into Delta with metadata (`ingest_ts`, `source_file`, `data_quality_flags`).
  - Silver: implement data cleaning rules, deduplication logic, schema standardization.
  - Gold: create 3 business marts (`sales_daily`, `product_performance`, `customer_360`).
  - Create comprehensive documentation: step-by-step notebook execution order, data transformation logic explanations, and visual schema showing how raw data flows through each layer.
  - **Fabric integration**: Export Gold tables to Fabric Lakehouse via Parquet files.
- **Data Analysts (DA)**
  - Build 3 interactive dashboards in Databricks notebooks (using plotly/matplotlib):
    - Executive KPI dashboard (GMV trends, AOV by country, return rates).
    - Customer segmentation analysis (RFM distribution, cohort retention).
    - Product performance dashboard (top categories, seasonal trends, inventory insights).
  - Perform data quality analysis comparing Raw vs Silver metrics.
  - Define business rules for churn (present findings to DS team).
  - **Fabric integration**: Export key visualizations to Power BI for stakeholder consumption.
- **Data Scientists (DS)**
  - Engineer 5+ features from Silver/Gold tables (RFM, seasonality, basket diversity).
  - Define and validate churn target using business logic from DA team.
  - Train 2 advanced models in MLflow (Support Vector Machine + Gradient Boosting with custom hyperparameters).
  - Generate churn scores and integrate back into Gold layer.
  - Create model performance documentation with evaluation metrics and business impact analysis.
  - Implement feature importance analysis to explain model predictions and business insights.

### Stretching (ambitious)
- **Data Engineers (DE)**
  - Implement automated data quality checks with custom assertions (row counts, uniqueness, business rules).
  - Create Delta Lake time-travel demo with version history and rollback capabilities.
  - Build a "data catalog" notebook documenting all tables, schemas, and dependencies.
- **Data Analysts (DA)**
  - Develop advanced cohort analysis and customer lifetime value calculations.
  - Create interactive drill-down capabilities in dashboards (country → region → customer).
  - Simulate A/B testing scenarios on historical data (e.g., impact of discount campaigns on customer segments, comparing conversion rates between different pricing strategies).
- **Data Scientists (DS)**
  - Implement advanced feature engineering (seasonal patterns, customer similarity).
  - Train and compare 4+ models (Neural Networks, CatBoost, ensemble stacking) with hyperparameter tuning.
  - Create model interpretability analysis (SHAP values, feature importance).
  - Build a simple model monitoring system tracking drift in input features.

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
  - Build advanced ensemble model combining multiple algorithms with custom weighting strategies.
  - Create automated model retraining logic based on performance degradation.

### Hire Me Level (Portfolio & Personal Branding)
- **Data Engineers (DE)**
  - **Production-Ready Pipeline**: Containerize the entire Databricks pipeline using Docker, implement CI/CD with GitHub Actions for automated testing and deployment.
  - **Technical Documentation**: Create comprehensive data architecture documentation with visual lineage diagrams (Bronze → Silver → Gold flow charts).
  - **Thought Leadership**: Publish technical blog post "Building Scalable Data Architecture with Databricks & Fabric" on LinkedIn or Medium.
- **Data Analysts (DA)**
  - **Executive Reporting Suite**: Build professional Power BI reports with advanced DAX calculations, automated refresh schedules, and mobile-responsive design.
  - **Business Storytelling**: Create compelling data story presentation showing EuroStyle's transformation journey with before/after metrics.
  - **Communication Skills**: Record professional case study presentation demonstrating business impact and ROI of data-driven decisions.
- **Data Scientists (DS)**
  - **MLOps Production API**: Deploy churn prediction model as REST API using FastAPI/Flask, containerized with Docker and deployed on Azure Container Apps or Streamlit Cloud. Include health checks, request/response logging, and model performance monitoring.
  - **Research & Innovation**: Conduct comparative analysis of traditional ML vs AutoML platforms (Azure ML, DataRobot) with performance benchmarks.
  - **Industry Case Study**: Write comprehensive white paper "From Excel to AI: A Retail Digital Transformation Case Study" showcasing technical and business outcomes.
- **All Profiles (Personal Branding)**
  - **LinkedIn Portfolio**: Optimize LinkedIn profile with project highlights, key achievements, and lessons learned summary.
  - **GitHub Showcase**: Create professional repository with detailed README, code documentation, and project architecture diagrams.

---

## References

### Academic Sources

**Just-in-Time Learning (JIT Learning)**
- Hug, T. (2016). *Microlearning: A New Pedagogical Challenge*. In Microlearning: Emerging Concepts, Practices and Technologies (pp. 7-12). Waxmann.
- Kapp, K. M., & Defelice, R. A. (2019). *Microlearning: Short and Sweet*. ATD Press.

**Microlearning**
- Shail, M. S. (2019). Using micro-learning on mobile applications to increase knowledge retention and work performance. *Journal of Applied Learning & Teaching*, 2(1), 2-12.
- Buchem, I., & Hamelmann, H. (2010). Microlearning: A strategy for ongoing professional development. *eLearning Papers*, 21, 1-15.

**Learning by Doing / Constructivism**
- Lave, J., & Wenger, E. (2021). *Situated Learning: Legitimate Peripheral Participation* (30th Anniversary Edition). Cambridge University Press.
- Kolb, D. A. (2014). *Experiential Learning: Experience as the Source of Learning and Development* (2nd Edition). Pearson Education.

**Scaffolding (Educational Scaffolding)**
- Van de Pol, J., Volman, M., & Beishuizen, J. (2010). Scaffolding in teacher-student interaction: A decade of research. *Educational Psychology Review*, 22(3), 271-296.
- Reiser, B. J. (2004). Scaffolding complex learning: The mechanisms of structuring and problematizing student work. *Journal of the Learning Sciences*, 13(3), 273-304.

**Problem-Based Learning (PBL)**
- Savery, J. R. (2015). Overview of problem-based learning: Definitions and distinctions. *Essential Readings in Problem-Based Learning*, 9, 5-15.
- Gijbels, D., Dochy, F., Van den Bossche, P., & Segers, M. (2005). Effects of problem-based learning: A meta-analysis from the angle of assessment. *Review of Educational Research*, 75(1), 27-61.

**Agile Learning**
- Dove, A., & Honigsfeld, A. (2018). *Co-teaching for English Learners: A Guide to Collaborative Planning, Instruction, Assessment, and Reflection*. Corwin Press.
- Rigby, D. K., Sutherland, J., & Takeuchi, H. (2016). Embracing agile. *Harvard Business Review*, 94(5), 40-50.
- Conboy, K. (2009). Agility from first principles: Reconstructing the concept of agility in information systems development. *Information Systems Research*, 20(3), 329-354.
