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
- **Industry**: Health insurance provider serving 5 major Saudi cities (Riyadh, Jeddah, Makkah, Medina, Dammam).  
- **Active Years**: Operating since 2010, processing ~16,700 claims monthly.  
- **Business Model**: B2B2C partnerships with 1,000+ healthcare providers.  
- **Key Metrics**:  
  - **SAR 251.6M** total claimed amount (2022–2025)  
  - **89.02%** average approval rate  
  - **SAR 44.8M** estimated profit (20% margin)  

**Analyst Context**:  
Claims data reveals systemic inefficiencies:  
- 93.25% of claims submitted >45 days after service (avg. 366 days lag)  
- **SAR 5.67M** paid for non-compliant denied claims (critical audit risk)  
- 10,038 underpaid claims (20.1% of total)  

---

### **3. Project Goals**  
**Strategic Imperatives**:  
1. **Reduce Financial Leakage**: Address **SAR 5.67M** in improper payments for denied/non-compliant claims.  
2. **Accelerate Processing**: Eliminate 1-year submission lags causing cash flow issues.  
3. **Improve Compliance**: Resolve 2,583 unjustified denials of compliant claims.  
4. **Optimize Adjudication**: Fix dental claim underpayments (100% due to invalid code logic).  

---

### **4. Data Collection & Sources**  
| **Source**               | **Scope**                                  | **Timeframe**       |  
|--------------------------|--------------------------------------------|---------------------|  
| Claims Database (SQL)    | 50,000 claims (medical/dental)             | 2022-06-05 to 2025-07-04 |  


---

### **5. Formal Data Governance**  
**Existing Framework**:  
- **ISO 27001** for data security  
- **HIPAA-equivalent** (Saudi NCA E-Health Standards) for PHI encryption  
- **Automated Checks**: Null validation for `claim_id`, `member_id`, `service_date`  

**Improvement Recommendations**:  
1. **Standardize Code Validation**:  
   - Accept ADA codes for dental claims (fixes 5,033 underpayments).  
2. **Implement Data Quality SLA**:  
   - Reject claims with missing `icd_code`/`cpt_code` (25,060 nulls).  
3. **Audit Trail Enhancement**:  
   - Log all `adjudicator_id` overrides of compliance flags.  

---

### **6. Regulatory Reporting**  
To be aligned with:
- **Saudi Council of Health Insurance (SCHI)** guidelines  
- Reporting on **timeliness**, **denial reasons**, and **compliance rates**  
- Preparation for external audits by **CCHI**, **NCA**, and **MoH**  

---

### **7. Methodology**  
**Analytical Approach**:  
1. **Time Series Analysis**: Claim lifecycle metrics (`submission_lag`, `processing_time`).  
2. **Root Cause Analysis**: Underpaid claims using `denial_reason` segmentation.  
3. **Geospatial Mapping**: Claim status by city/diagnosis (ICD code hotspots).  
4. **Compliance Reconciliation**: Cross-filter `claim_status`, `is_compliant`, and `approved_amount`.  

**Tools**:  
- **Python Pandas**: For `submission_lag = submission_date - service_date`  
- **Statistical Thresholds**: "Late" = >45 days (per internal SLA)  

---

### **8. Data Structure & Initial Checks**  
**Primary Table**: `claims` (50,000 rows, 18 columns)  

**KeyColumns:

| **Column**          | **Description**                     | **Critical Issue**               |  
|---------------------|-------------------------------------|----------------------------------|  
| `service_date`      | Date of healthcare service          | Min: 2022-06-05, Max: 2025-05-31|  
| `submission_date`   | Date claim filed by provider        | 93.25% >45 days late            |  
| `decision_date`     | Date insurer finalized claim        | Avg. processing: 15.5 days       |  
| `denial_reason`     | Why claim was reduced/denied        | 39,962 nulls (79.92%)           |  
| `icd_code`/`cpt_code`| Diagnosis/procedure codes           | 25,060 nulls (50.12%)           |  
| `is_compliant`      | Whether claim met rules             | 4,000+ approved claims non-compliant |  

---

### **9. Documenting Issues**  
| **Table** | **Column**        | **Issue**                              | **Magnitude** | **Solvable** | **Resolution**                    |  
|-----------|-------------------|----------------------------------------|---------------|--------------|-----------------------------------|  
| claims    | `denial_reason`   | Null in 79.92% of underpaid claims     | High          | Yes          | Mandate field entry at submission |  
| claims    | `icd_code`        | 50.12% nulls (invalid for medical)     | High          | Yes          | ADA code parity for dental claims |  
| claims    | `is_compliant`    | Approved claims marked non-compliant   | Critical      | Yes          | Reconcile with adjudication rules |  
| claims    | `claim_status`    | "Denied" claims with paid amounts      | Critical      | Yes          | Block payments if status=Denied   |  

---

### **10. Executive Summary**  
**For CFO & COO**:  
1. **SAR 105.33M at Risk**:  
   - **SAR 5.67M** paid for non-compliant denied claims (immediate refund action required).  
   - **SAR 99.66M** approved despite non-compliance (governance failure).  
2. **Operational Delays**: 93.25% claims submitted >45 days late (avg. 366 days).  
3. **Dental Claims Crisis**: 100% underpaid due to invalid CPT/ICD logic (fix: accept ADA codes).  

---

### **11. Insights Deep Dive**  
#### **Category 1: Financial Leakage**  
- **Insight**: **SAR 5.67M** disbursed for claims marked `Denied + is_compliant=False`.  
- **Insight**: 49.53% of approved amounts (**SAR 99.66M**) were non-compliant.  
- **Recommendation**: Freeze these payments and audit adjudicator IDs P168 and P853.  

#### **Category 2: Timeliness**  
- **Insight**: 93.25% claims submitted >45 days late (up to 1,095 days).  
- **Insight**: Riyadh has highest average lifecycle time: 385 days.  
- **Recommendation**: EHR integration and penalties for late submissions.  

#### **Category 3: Claim Accuracy**  
- **Insight**: 5,033 dental claims underpaid due to ADA code rejection.  
- **Insight**: Most common denials — "Over Limit" (2,564), "Not Covered" (2,561).  
- **Recommendation**: Accept ADA codes; review benefit design logic.  

#### **Category 4: Geographic Hotspots**  
- **Insight**: Makkah leads in cholera (A00) denials, pending goiter (E04) claims.  
- **Insight**: Melanoma (D03) denials highest in Riyadh.  
- **Recommendation**: Targeted coding training for providers.  

---

### **12. Recommendations**  
1. **Financial Controls**  
   - Reverse **SAR 5.67M** in non-compliant denied claim payouts.  
   - Block payments where `claim_status = "Denied"`.  
2. **Dental Claims Fix**  
   - Accept ADA codes to stop 100% underpayments.  
3. **Provider Training**  
   - Prioritize adjudicator training (P168, P853).  
4. **Compliance Logic Audit**  
   - Investigate non-compliant approved claims (~SAR 99.66M).  
5. **Timeliness SLA**  
   - Penalize claims >5 days late; enable EHR auto-submission.  

---

### **13. Future Work**  
- **ML Flagging**: Build model to predict non-compliant or high-risk claims.  
- **Scorecards**: Monthly report cards for provider accuracy and timeliness.  
- **Policy Mapping**: Align `is_compliant` logic with SCHI standards.  

---

### **14. Technical Details**  
**Tools Used**:  
- **Python (Pandas)**: Time calculations, null detection  


### **15. Assumptions and Caveats**
- **Date Integrity**: Claims before *2022-06-05* were excluded as out-of-scope per business rules.
- **ICD Validity**: Diagnosis codes (`icd_code`) were assumed valid if non-null, although 25,000+ records had missing values.
- **Compliance Logic**: The `is_compliant` field was presumed accurate, despite observable inconsistencies with claim status and payment outcomes.
- **Profit Estimate**: A flat 20% profit margin was applied to all claims; actual margins may vary depending on provider contracts and negotiated rates.
