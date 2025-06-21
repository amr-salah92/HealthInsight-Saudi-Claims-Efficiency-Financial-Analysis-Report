### Comprehensive Claims Analysis Report for Saudi Health Insurer 

---

#### **Table of Contents**  
1. [Project Name](#1-project-name)  
2. [Project Background](#2-project-background)  
3. [Project Goals](#3-project-goals)  
4. [Data Collection and Sources](#4-data-collection--sources)  
5. [Formal Data Governance](#5-formal-data-governance)  
6. [Regulatory Reporting](#6-regulatory-reporting)  
7. [Methodology](#7-methodology)  
8. [Data Structure & Initial Checks](#8-data-structure--initial-checks)  
9. [Documenting Issues](#9-documenting-issues)  
10. [Executive Summary](#10-executive-summary)  
11. [Insights Deep Dive](#11-insights-deep-dive)  
12. [Recommendations](#12-recommendations)  
13. [Future Work](#13-future-work)  
14. [Technical Details](#14-technical-details)  
15. [Assumptions and Caveats](#15-assumptions-and-caveats)  

---

### **1. Project Name**  
**Saudi National Health Insurance (SNHI) Claims Efficiency & Compliance Audit**  
*Analysis of 50,000 medical/dental claims (June 2022–July 2025) to optimize adjudication, reduce financial leakage, and ensure regulatory compliance.*  

---

### **2. Project Background**  
**Company Profile**:  
- **Industry**: Health insurance provider serving 5 major Saudi cities  
- **Active Years**: Operating since 2010, processing ~16,700 claims monthly  
- **Key Metrics**:  
  - **SAR 251.6M** total claimed amount  
  - **89.02%** average approval rate  
  - **SAR 44.8M** estimated profit (20% margin)  

**Critical Findings**:  
- **SAR 103.07M** underpayment reductions from 10,038 claims  
- **SAR 5.67M** paid for non-compliant denied claims  
- 93.25% claims submitted >45 days after service  

---

### **3. Project Goals**  
**Strategic Imperatives**:  
1. **Recover SAR 5.67M** in improper payments for denied claims  
2. **Accelerate processing** by eliminating 1-year submission lags  
3. **Prevent SAR 103M+** in underpayment disputes  
4. **Resolve compliance gaps** in 19,898 approved non-compliant claims  

---

### **4. Data Collection & Sources**  
| **Source**               | **Scope**                                  | **Timeframe**       |  
|--------------------------|--------------------------------------------|---------------------|  
| Claims Database (SQL)    | 50,000 claims (medical/dental)             | 2022-06-05 to 2025-07-04 |  

---

### **5. Formal Data Governance**  
**Critical Improvements**:  
1. **Payment Validation Rules**:  
   - Block payments if `claim_status = "Denied"`  
   - Require executive override for `is_compliant = False` approvals  
2. **Underpayment Logic**:  
   - Implement reason-specific reduction rules  
3. **ICD Code Enforcement**:  
   - Reject claims with null `icd_code`/`cpt_code` (25,060 records)  

---

### **6. Regulatory Reporting**  
**Alignment Requirements**:  
- **SCHI** guidelines for claim timeliness  
- **NCA** compliance standards  
- **MoH** auditing frameworks  

---

### **7. Methodology**  
**Enhanced Analytical Approach**:  
1. **Compliance Reconciliation**: Cross-filtered `claim_status`, `is_compliant`, and `approved_amount`  
2. **Root Cause Analysis**: Segmented underpayments by denial reason  
3. **Geospatial Hotspots**: Mapped city/ICD denial patterns  
4. **Lifecycle Trend Analysis**: Submission lag vs. processing time  

---

### **8. Data Structure & Initial Checks**  
**Critical Columns**:  
| **Column**          | **Critical Issue**               | **Impact**              |  
|---------------------|----------------------------------|-------------------------|  
| `denial_reason`     | 39,962 nulls (79.92%)           | Undermines RCA accuracy |  
| `is_compliant`      | 4,000+ non-compliant approvals  | SAR 99.66M at risk      |  
| `claim_status`      | Paid denials (5,090 claims)     | SAR 5.67M leakage       |  
| `icd_code`          | 25,060 nulls (50.12%)           | Invalidates 50% of data |  

---

### **9. Documenting Issues**  
| **Table** | **Column**        | **Issue**                              | **Resolution**                    |  
|-----------|-------------------|----------------------------------------|-----------------------------------|  
| claims    | `is_compliant`    | Approved non-compliant claims          | Integrate with approval workflows |  
| claims    | `claim_status`    | "Denied" claims with paid amounts      | Block payment system integration  |  
| claims    | `denial_reason`   | Null in underpaid claims               | Mandatory field at submission     |  

---

### **10. Executive Summary**  
**Critical Risks & Opportunities**:  
1. **Financial Leakage (SAR 108.74M)**:  
   - **SAR 5.67M**: Paid for non-compliant denied claims  
   - **SAR 103.07M**: Underpayment reductions at dispute risk  
2. **Compliance Breaches**:  
   - 19,898 non-compliant claims fully approved (SAR 99.66M)  
3. **Operational Improvements**:  
   - Submission lags reduced from 500 to 100 days (2022-2025)  
   - Processing time halved (150 → 50 days)  

---

### **11. Insights Deep Dive**  
#### **Category 1: Financial Leakage & Underpayments**  
- **SAR 5.67M** paid for denied claims (5,090 records)  
- **SAR 103.07M** underpayment reductions from 10,038 claims:  
  - **Uniform reductions**: ~SAR 10,270/claim regardless of denial reason  
  - **Top drivers**: Over Limit (SAR 26.6M), Not Covered (SAR 26.0M)  
- **Compliance Gaps**: 19,898 non-compliant claims approved & paid  

#### **Category 2: Timeliness & Efficiency**  
- **Submission Lag**:  
  - 93.25% claims >45 days late (avg. 366 days)  
  - Improved from 500 days (2022) to 100 days (2025)  
- **Processing Time**:  
  - Reduced from 150 days (Jan 2023) to 50 days (Jan 2025)  

#### **Category 3: Claim Accuracy & Denials**  
- **Underpayment Root Causes**:  
  | **Reason**       | **Claims** | **Reduction (SAR)** |  
  |------------------|------------|---------------------|  
  | Over Limit       | 2,564      | 26.58M              |  
  | Not Covered      | 2,561      | 26.03M              |  
  | Invalid Code     | 2,473      | 25.67M              |  
  | Incomplete Info  | 2,440      | 24.78M              |  
- **Dental Claims**: 5,033 underpaid due to ADA code rejection  

#### **Category 4: Geographic & Clinical Hotspots**  
- **City Risk**:  
  - **C02**: Highest denials (524), all ICDs >100 denials  
  - **E04**: Extreme D05 denials (117)  
- **ICD Risk**:  
  - **B04**: 536 denials (all cities)  
  - **D05**: 515 denials (E04 hotspot)  

---

### **12. Recommendations**  
**Immediate Actions (0-3 Months)**:  
1. **Recover SAR 5.67M**: Freeze payments for denied claims and initiate refunds  
2. **Revise Underpayment Logic**:  
   - Replace flat reductions with reason-specific rules  
   - e.g., "Incomplete Info" → 50% payment, "Over Limit" → 80% payment  
3. **Compliance Integration**: Block approvals when `is_compliant=False`  

**System & Process Improvements**:  
- **Provider Education**:  
  - Target ICD B04/D05 coding errors (C02/E04 cities)  
  - Train on ADA requirements for dental claims  
- **Automated Controls**:  
  - Payment block if `claim_status="Denied"`  
  - Daily reconciliation reports for compliance categories  

**Financial Impact**:  
| **Initiative**               | **Savings/Recovery** |  
|------------------------------|----------------------|  
| Fix denied claim payments    | SAR 5.67M immediate |  
| Reduce "Invalid Code" errors | SAR 25.67M annually |  
| Optimize "Over Limit" logic  | SAR 5.32M annually  |  

---

### **13. Future Work**  
- **ML Predictive Analytics**: Flag high-risk claims pre-adjudication  
- **Provider Performance Scorecards**: Accuracy/timeliness benchmarks  
- **Benefit Design Review**: Update coverage limits for frequently exceeded services  

---

### **14. Technical Details**  
**Key Analyses**:  
- **Compliance Reconciliation**: 6-category payment/compliance segmentation  
- **Root Cause Analysis**: Denial reason segmentation of underpayments  
- **Geospatial Heatmaps**: City/ICD denial hotspots  

---

### **15. Assumptions and Caveats**  
- **Underpayment Risk**: SAR 103.07M represents potential provider disputes  
- **ICD Completeness**: 50.12% null rates limit clinical insights  
- **Currency**: USD conversions at 1 USD = 3.75 SAR  
- **Timeframe**: Claims outside 2022-2025 excluded from analysis  

