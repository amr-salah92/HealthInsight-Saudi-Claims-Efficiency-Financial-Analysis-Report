### **HealthInsight Saudi: Claims Efficiency & Financial Analysis Report**  

---

#### **Table of Contents**  
1. [**Project Name**](#1-project-name)  
2. [**Project Background**](#2-project-background)  
3. [**Project Goals**](#3-project-goals)  
4. [**Insights & Recommendations Categories**](#4-insights--recommendations-categories)  
5. [**Data Collection & Sources**](#5-data-collection--sources)  
6. [**Formal Data Governance**](#6-formal-data-governance)  
7. [**Regulatory Reporting**](#7-regulatory-reporting)  
8. [**Methodology**](#8-methodology)  
9. [**Data Structure & Initial Checks**](#9-data-structure--initial-checks)  
10. [**Documenting Issues**](#10-documenting-issues)  
11. [**Executive Summary**](#11-executive-summary)  
12. [**Insights Deep Dive**](#12-insights-deep-dive)  
13. [**Recommendations**](#13-recommendations)  
14. [**Future Work**](#14-future-work)  
15. [**Technical Details**](#15-technical-details)  
16. [**Assumptions & Caveats**](#16-assumptions--caveats)  

---

### **1. Project Name**  
**HealthInsight Saudi: Claims Adjudication Efficiency & Financial Impact Analysis**  

---

### **2. Project Background**  
HealthInsight Saudi is a health insurance provider operating in Saudi Arabia since 2018, serving 500K+ members across 5 major cities (Riyadh, Jeddah, Makkah, Medina, Dammam). The company processes ~50,000 claims monthly, with a total claimed amount of **SAR 251.6M** and approved amount of **SAR 224.1M** (89.02% approval rate) between June 2023–July 2025. Key business metrics include:  
- **Claim Submission Lag**: 99.23% of claims submitted >5 days after service.  
- **Processing Delays**: 66.67% of claims processed >10 days after submission.  
- **Denial Rate**: 10.98% (10,038 underpaid/denied claims).  

---

### **3. Project Goals**  
As a data analyst at HealthInsight, this analysis aims to:  
1. **Reduce operational costs** by identifying root causes of claim denials (10.98% rate impacting SAR 27.5M).  
2. **Improve provider compliance** by resolving submission lags (99.23% exceeding 5-day SLA).  
3. **Enhance regulatory adherence** by auditing inconsistent `claim_status` logic (e.g., compliant claims denied with payouts).  
4. **Optimize adjudication efficiency** by automating high-delay processes (66.67% exceeding 10-day processing).  

---

### **4. Insights & Recommendations Categories**  
- **Category 1**: Claim Processing Efficiency (Delays & SLA Breaches)  
- **Category 2**: Financial Leakage (Denials & Underpayments)  
- **Category 3**: Data Quality & Coding Integrity  
- **Category 4**: Geographic/Diagnosis-Specific Trends  

---

### **5. Data Collection & Sources**  
- **Primary Source**: Internal claims database (June 2022–July 2025).  
- **Key Tables**:  
  - `claims_fact`: Service dates, amounts, status, adjudicator IDs.  
  - `diagnosis_codes`: ICD codes (e.g., B01, D03), categories.  
  - `procedure_codes`: CPT/ADA codes (e.g., 99201, D1110).  
  - `provider_dim`: Provider IDs, cities, performance metrics.  
- **External Data**: Regulatory SLA guidelines (Saudi Council of Health Insurance).  

---

### **6. Formal Data Governance**  
- **Standardization**: Enforced ICD-10/CPT-4 coding for medical claims; ADA for dental.  
- **Issues**:  
  - **Dental Claims**: 5,033 records missing CPT/ICD codes (expected per industry standards).  
  - **Nulls**: 25K+ missing ICD/CPT codes; 39,962 missing `denial_reason`.  
- **Improvement Framework**: Implement data validation rules to accept ADA codes without flagging as "Invalid Code."  

---

### **7. Regulatory Reporting**  
- **Compliance Gaps**:  
  - 20% of compliant claims (`is_compliant=1`) marked as "Denied" but paid (`approved_amount>0`), violating audit trails.  
  - **Action**: Align `claim_status` with financial transactions to meet Saudi Billing Compliance Act (2024).  

---

### **8. Methodology**  
- **Metrics Calculation**: Time-based lags (e.g., `decision_date - service_date`).  
- **Statistical Analysis**:  
  - Denial reason distribution (chi-square tests).  
  - Geographic/diagnosis trends (cluster analysis).  
- **Tools**: Python (pandas, scikit-learn), SQL for data aggregation.  

---

### **9. Data Structure & Initial Checks**  
**Dataset**: 50,000 claims (June 2022–July 2025).  
**Key Columns**:  
- `claim_id`, `member_id`, `provider_id`  
- `service_date` (min: 2022-06-05; max: 2025-05-31)  
- `submission_date` (min: 2023-06-05; max: 2025-06-04)  
- `decision_date` (min: 2023-06-06; max: 2025-07-04)  
- `claim_status`: 20% mismatched with financial outcomes.  
- `denial_reason`: 39,962 nulls (79.8% of underpaid claims).  

**Initial Checks**:  
- 0 duplicate rows.  
- Critical nulls: `icd_code` (25,060), `cpt_code` (25,060), `ada_code` (24,940).  

---

### **10. Documenting Issues**  
| Table         | Column          | Issue                              | Magnitude | Solvable | Resolution                          |  
|---------------|-----------------|------------------------------------|-----------|----------|-------------------------------------|  
| claims_fact   | denial_reason   | 39,962 nulls (79.8% underpaid)     | High      | Yes      | Add mandatory field in submission UI|  
| claims_fact   | claim_status    | Misaligned with approved amounts   | High      | Yes      | Audit adjudication logic            |  
| diagnosis_dim | icd_code        | 25,060 nulls (50.1% of underpaid)  | Medium    | Yes      | Provider training on coding        |  
| procedure_dim | cpt_code        | 25,060 nulls (dental claims use ADA)| Medium   | Yes      | Accept ADA codes for dental        |  

---

### **11. Executive Summary**  
**Stakeholder**: CFO & Operations Director  
**Top 3 Insights**:  
1. **SAR 27.5M in claim reductions** due to systemic coding errors (e.g., dental claims using ADA codes flagged as "Invalid").  
2. **99.23% late submissions** (avg. 1-year lag) delaying reimbursements and risking SLA penalties.  
3. **Riyadh & Makkah** have highest claim denials for high-cost diagnoses (melanoma, cholera).  

---

### **12. Insights Deep Dive**  

#### **Category 1: Claim Processing Efficiency**  
- **Insight 1**: 99.23% claims submitted >5 days post-service (avg. 1 year).  
  - **Impact**: SAR 8.2M/month delayed cash flow.  
- **Insight 2**: 66.67% claims processed >10 days; peaks in Makkah (117 pending E04 goiter claims).  
- **Visual**: _[Time-series graph of lags by city]_  

#### **Category 2: Financial Leakage**  
- **Insight 1**: 10.98% denial rate (SAR 27.5M), driven by "Over Limit" (2,564) and "Not Covered" (2,561).  
- **Insight 2**: 5,033 dental claims underpaid due to missing CPT/ICD (fixed by accepting ADA).  
- **Visual**: _[Pie chart of denial reasons]_  

#### **Category 3: Data Quality**  
- **Insight 1**: 50.1% underpaid claims missing ICD/CPT codes (providers P168, P853 highest).  
- **Insight 2**: 20% of "Denied" claims paid (`approved_amount>0`), creating audit risks.  
- **Visual**: _[Heatmap of missing codes by provider]_  

#### **Category 4: Geographic Trends**  
- **Insight 1**: **Cholera (A00)** denials highest in Dammam (1,029) due to sanitation-linked outbreaks.  
- **Insight 2**: **Melanoma (D03)** pending claims peak in Riyadh (117) – diagnostic complexity delays.  
- **Visual**: _[Bar chart of top ICD codes by city]_  

---

### **13. Recommendations**  
1. **Revise Dental Claim Validation**  
   - Accept ADA codes without CPT/ICD to prevent 5,033 false underpayments.  
2. **Automate High-Delay Processes**  
   - Deploy AI adjudication for low-risk claims to reduce 66.67% processing delays.  
3. **Provider Training**  
   - Target P168, P853 for coding compliance (e.g., "Invalid Code" caused 2,473 denials).  
4. **Align `claim_status` with Payouts**  
   - Fix logic to avoid "Denied but paid" conflicts (20% of compliant claims).  
5. **Real-Time EHR Submission**  
   - Integrate with hospital systems to eliminate 1-year submission lags.  

---

### **14. Future Work**  
1. **Predictive Denial Modeling**: ML model to flag high-risk claims pre-submission.  
2. **Provider Scorecards**: Track coding accuracy by provider.  
3. **ICD-Cost Correlation**: Identify high-cost diagnoses (e.g., melanoma) for reserve planning.  

---

### **15. Technical Details**  
- **Tools**: Python (pandas, Matplotlib), SQL, Tableau.  
- **SQL Queries**: _[Link to queries]_  
- **Tableau Dashboard**: _[Link to dashboard]_  
- **Why Python**: Efficient handling of 50K+ records; scikit-learn for future ML integration.  

---

### **16. Assumptions & Caveats**  
1. **Late Submission Threshold**: Used >5 days (per internal SLA) – actual regulatory SLA may differ.  
2. **Dental Claims**: Assumed ADA codes are valid without CPT/ICD (industry standard).  
3. **"Denied" Claims with Payouts**: Assumed data entry errors – actual root cause needs audit.  
4. **Geographic Trends**: City-level diagnosis volume tied to population density (not adjusted for risk).  

---  
**Prepared by**: Data Analytics Team | HealthInsight Saudi  
**Date**: June 20, 2025
