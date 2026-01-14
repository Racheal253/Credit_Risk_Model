# End-to-End Credit Risk Analytics Platform📖 

## Project Overview  
This project is an enterprise-grade data engineering and machine learning solution designed to assess and predict retailer credit risk.Built on Microsoft Fabric, it implements a scalable Medallion Architecture to process transaction data, enrich it with ML-generated "Default Probability" scores, and serve insights via a Power BI dashboard.The system transforms risk management from reactive (analyzing past defaults) to proactive (predicting future risk), enabling the business to mitigate bad debt exposure in real-time.

---

## 🏗️ Architecture
The solution follows a strict Medallion Architecture (Bronze $\rightarrow$ Silver $\rightarrow$ Gold) orchestrated by Microsoft Fabric Pipelines.The Data FlowBronze Layer (Raw): Ingests raw transaction logs and retailer data into the Data Lakehouse.Silver Layer (Validated): Cleans data, handles nulls/duplicates, and enforces data types.ML Layer (Intelligence):Generates features (e.g., Average Order Value, Payment Latency).Runs a predictive model to assign a Risk Score (0-100) and Risk Segment (e.g., "High Risk") to every retailer.Gold Layer (Curated):Integrates ML scores into the business data.Models a Star Schema with dynamic dimensions (Geography, Shop Type) for high-performance reporting.Consumption: Automated Power BI Semantic Model refresh for dashboarding.

<img width="1437" height="333" alt="Screenshot 2026-01-14 135029" src="https://github.com/user-attachments/assets/7b469290-19f2-4446-919f-d499c4dad306" />

---

## 🛠️ Tech StackPlatform:
- Microsoft Fabric / AzureCompute: Spark (PySpark)
- Storage: Delta Lake
- Orchestration: Fabric Data Pipelines (ADF)
- Machine Learning: MLflow, Scikit-Learn (Classification Model)
- Visualization: Power BI
- Language: Python, SQL

---

## 📂 Project Structure & Notebooks
The pipeline consists of 5 sequential notebooks managed by a master orchestrator.

| Sequence | Notebook Name | Description |
| :---: | :--- | :--- |
| **1** | `bronze ingestion` | Ingests raw source data into Delta Tables. |
| **2** | `silver_cleaning` | Standardizes formats, removes duplicates, and validates schema. |
| **3** | `gold ml_features` | Engineers features required for the model (e.g., Days Late, Order Frequency). |
| **4** | `credit risk modelling` | Trains/Inferences the ML model. Outputs `ml_retailer_scores_final`. |
| **5** | `notebook normalized table` | **(Core Engineering)** Joins Business Data + ML Scores. Creates the Star Schema. |

---

## 📊 Data Model (Star Schema)
The Gold Layer is modeled as a Star Schema to optimize Power BI performance.

### **Fact Table**
* **`fact_transactions_with_risk`**
  * The central table containing all transaction details, enriched with foreign keys and risk snapshots.
  * **Columns:** `transaction_id`, `amount`, `risk_score_key`, `credit_segment_key`, `geography_key`, `is_high_risk_transaction`.

### **Dimension Tables**
* **`dim_retailers`**: Retailer profiles (Name, Gender, Age).
* **`dim_geography`**: Dynamically generated location data (State, Region).
* **`dim_shop_type`**: Business categories (Pharmacy, Grocery, etc.).
* **`dim_credit_segments`**: Business rules for credit tiers (Gold, Silver, Bronze).
* **`dim_risk_scores`**: Detailed ML model outputs and probability histories.

---

## 🚀 Key Features
* **Dynamic Dimension Generation:** The pipeline automatically identifies new States or Shop Types and generates their Dimension Keys on the fly.
* **ML-Integrated ETL:** "Risk" is treated as a first-class data citizen. Every transaction is stamped with the retailer's risk profile *at the moment of the transaction*.
* **Source-of-Truth Key Lookup:** Logic ensures that the Fact Table strictly adheres to the keys defined in the `dim_credit_segments` table, preventing data integrity issues.
* **Automated Orchestration:** A single pipeline trigger runs the entire workflow from Ingestion $\rightarrow$ Dashboard Refresh.

---

## 📈 Business Impact
* **Proactive Risk Mitigation:** Transformed risk management by enriching **100% of transactions** with ML-generated "Default Probability" scores.
* **Financial Protection:** Empowered the finance team to identify high-risk exposure instantly, potentially reducing bad debt by blocking high-risk credit extensions.
* **Operational Efficiency:** Automated the end-to-end data lifecycle, reducing manual reporting latency by **90%** (from weekly manual extracts to daily automated dashboards).

---

## 🔄 How to Run
1. **Prerequisites:** Ensure Microsoft Fabric Capacity is active and the Lakehouse is configured.
2. **Trigger:** Navigate to the **"Orchestrator Pipeline"** in the workspace.
3. **Execute:** Click **Run**. The pipeline will sequentially execute all notebooks and refresh the Power BI dataset upon success.
4. **Monitor:** Check the "Run History" tab for status updates or email notifications on failure.
