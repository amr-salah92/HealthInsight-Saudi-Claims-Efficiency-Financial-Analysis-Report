### **HealthInsight Saudi: Claims Efficiency & Financial Analysis Report**  

---

#### **Table of Contents**  
1. [Project Name](#1-project-name)  
2. [Project Background](#2-project-background)  
3. [Project Goals](#3-project-goals)  
4. [Data Collection and Sources](#4-data-collection-and-sources)  
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
### **Comprehensive Claims Analysis Report for Saudi Health Insurer**  

---

### **1. Project Name**  
**Saudi National Health Insurance (SNHI) Claims Adjudication Efficiency & Compliance Audit**  

---

### **2. Project Background**  
SNHI operates in Saudi Arabia’s healthcare insurance sector (est. 2010), serving 5 major cities (Riyadh, Jeddah, Makkah, Medina, Dammam). Business metrics:  
- **50,000 claims** analyzed (service dates: Jun 2022–May 2025).  
- **SAR 251.6M** total claimed amount, **SAR 224.1M** approved.  
- **10,038 underpaid claims** (20% of total), **39,962 denials**.  
- Key processes: Claim submission → medical coding → adjudication → payout.  

---

### **3. Project Goals**  
**Why this analysis matters:**  
- **Financial**: 10.98% claim reduction rate (SAR 27.5M) requires root-cause diagnosis.  
- **Compliance**: SAR 5.67M paid for *denied+non-compliant* claims violates internal controls.  
- **Efficiency**: 99.23% late submissions and 66.67% slow processing risk SLA penalties.  

**Goals**:  
1. Identify causes of underpayments and denials.  
2. Resolve adjudication-compliance contradictions.  
3. Reduce claim cycle time by ≥30%.  

**Insights/recommendations cover**:  
- **Timeliness**: Submission/processing delays  
- **Financial Leakage**: Underpayment patterns  
- **Compliance**: Policy violations  
- **Diagnostic Trends**: ICD/CPT denial hotspots  

---

### **4. Data Collection and Sources**  
- **Primary Source**: Internal claims database (June 2022–July 2025).  
- **Tables**: `claims_fact` (transaction data), `icd_codes`, `cpt_codes`, `ada_codes` (reference tables).  
- **External Data**: None (internal system-only).  

---

### **5. Formal Data Governance**  
**Existing Standards**:  
- ICD-10/CPT-4 coding enforced.  
- Automated validation checks at submission.  

**Gaps**:  
1. **Dental Claims**: ADA codes rejected due to missing CPT/ICD fields (5,033 underpaid claims).  
2. **Compliance Flags**: `is_compliant` misaligned with adjudication logic (SAR 99.66M approved for non-compliant claims).  
3. **Date Integrity**: 364-day avg. submission lag indicates batch-load errors.  

**Improvement Framework**:  
- **DCAM v2.0**: Implement data quality scoring for claim fields (e.g., `denial_reason` completeness).  

---

### **6. Regulatory Reporting**  
**Compliance Focus**:  
- **Saudi Council of Health Insurance (SCHI)** regulations:  
  - Mandate ≤5-day claim submissions (99.23% non-compliant).  
  - Require audit trails for denied claims.  

**Actions**:  
- Reconcile `claim_status` vs. `approved_amount` (SAR 5.67M paid for denied claims flagged).  
- Document root causes for 2,583 compliant-but-denied claims.  

---

### **7. Methodology**  
- **Tools**: Python (pandas, Matplotlib), SQL.  
- **Techniques**:  
  - Temporal analysis: `submission_lag = submission_date - service_date`.  
  - Cohort analysis: Underpaid claims by provider/city.  
  - Compliance scoring: Logic checks between `claim_status`, `is_compliant`, and `approved_amount`.  

---

### **8. Data Structure & Initial Checks**  
**Dataset**: 50,000 claims (June 2022–July 2025).  

| **Table**       | **Columns**                          | **Description**                     | **Key Issues**                     |  
|-----------------|--------------------------------------|-------------------------------------|------------------------------------|  
| `claims_fact`   | `claim_id`, `service_date`, `decision_date`, `claim_amount`, `approved_amount`, `claim_status`, `is_compliant`, `city` | Core transactional data | 39,962 nulls in `denial_reason`; 36,000+ claims with >364-day submission lag |  
| `icd_codes`     | `icd_code`, `diagnosis_name`, `category` | ICD-10 reference data              | 25,060 claims missing ICD codes    |  
| `cpt_codes`     | `cpt_code`, `procedure_name`        | CPT-4 procedure codes              | 25,060 claims missing CPT codes    |  
| `ada_codes`     | `ada_code`, `procedure_name`        | Dental procedure standards         | 24,940 missing ADA codes; NaN handling needed |  

**Initial Checks**:  
- 0 duplicate rows.  
- Critical nulls: `denial_reason` (79.9%), `icd_code` (50.1%), `cpt_code` (50.1%).  
- Date ranges align with business cycles (no outliers).  

---

### **9. Documenting Issues**  
| **Table**      | **Column**         | **Issue**                          | **Magnitude** | **Solvable** | **Resolution**                          |  
|----------------|--------------------|------------------------------------|---------------|--------------|-----------------------------------------|  
| `claims_fact`  | `denial_reason`    | 79.9% nulls in denied claims      | High          | Yes          | Enforce dropdown menu at submission     |  
| `claims_fact`  | `submission_date`  | 364-day avg. lag (vs. 5-day SLA)  | Critical      | Yes          | Audit batch-load processes; automate EHR integrations |  
| `claims_fact`  | `is_compliant`     | 4,000 claims approved while non-compliant | High | Yes       | Reconcile with policy engine logs       |  
| `cpt_codes`    | `cpt_code`         | 50.1% nulls (dental claims use ADA) | Medium      | Yes          | Accept ADA codes when `claim_type = 'Dental'` |  
| `claims_fact`  | `approved_amount`  | SAR 5.67M paid for denied claims  | Critical      | Yes          | Block payouts when `claim_status = 'Denied'` |  

---

### **10. Executive Summary**  
**For CFO & COO**:  
1. **SAR 105.3M at risk**: SAR 99.66M (non-compliant approvals) + SAR 5.67M (denied-but-paid claims).  
2. **Operational delays**: 99.23% claims miss 5-day submission SLA; 66.67% exceed 10-day processing SLA.  
3. **Systemic coding failure**: 5,033 dental claims underpaid due to invalid CPT/ICD checks.  

![Cycle Time Breakdown](https://via.placeholder.com/400x200?text=Avg.+Cycle+Time=380+Days)  

---

### **11. Insights Deep Dive**  

#### **Category 1: Timeliness & Efficiency**  
- **99.23% claims submitted late** (avg. 368 days vs. 5-day SLA), worst in Riyadh (369 days).  
- **66.67% claims processed late** (avg. 15.5 days vs. 10-day SLA), peaking in Jeddah (15.6 days).  
- **Total cycle time**: 380 days end-to-end — exposes legal/regulatory risks.  
- **Root cause**: Batch-load submissions (not real-time EHR) and manual adjudication.  

![image](https://via.placeholder.com/600x300?text=Submission+Lag+by+City:+Riyadh=369d,+Jeddah=368d)  

#### **Category 2: Underpayment & Denial Patterns**  
- **10,038 underpaid claims** (20% of total):  
  - **Dental (50.1%)**: All due to missing CPT/ICD (used valid ADA codes).  
  - **Medical (49.9%)**: Top denial reasons: "Over Limit" (2,564), "Not Covered" (2,561).  
- **Providers P168, P853, P152**: Highest underpayment rates (20+ claims each).  
- **Missing codes**: 5,033 dental claims lacked CPT/ICD — system design flaw.  

![image](https://via.placeholder.com/600x300?text=Underpaid+Claims+by+Type:+Dental=50%25,+Medical=50%25)  

#### **Category 3: Compliance & Financial Control**  
- **Critical violations**:  
  - ⚠️ **SAR 5.67M** paid for claims marked `Denied` + `is_compliant=False`.  
  - ⚠️ **SAR 99.66M** approved for claims with `is_compliant=False` (49.53% of approved amount).  
- **Contradictions**: 2,583 compliant claims denied (`claim_status='Denied'` + `is_compliant=True`).  
- **Cities at risk**: Jeddah/Riyadh lead in non-compliant approvals.  

![image](https://via.placeholder.com/600x300?text=Compliance+Violations:+Jeddah=28%25,+Riyadh=27%25)  

#### **Category 4: Diagnosis & Procedure Trends**  
- **Top denied diagnoses**:  
  - **Cholera (A00)**: 112 denials in Makkah, 104 in Medina.  
  - **Melanoma (D03)**: 117 denials in Riyadh.  
- **Pending claims**: Goiter (E04) in Makkah (117), Chickenpox (B01) in Riyadh (105).  
- **Procedure issues**: Dental code `D2740` (crowns) frequently underpaid due to ADA-CPT mapping.  

![image](https://via.placeholder.com/600x300?text=Top+Denied+Diagnoses:+A00=112,+D03=117,+B01=116)  

---

### **12. Recommendations**  
1. **Automate claim submissions**:  
   - **Action**: Integrate EHR systems for real-time submissions.  
   - **Impact**: Reduce 368-day avg. lag to <5 days.  

2. **Fix dental claim validation**:  
   - **Action**: Accept ADA codes without CPT/ICD for dental claims.  
   - **Impact**: Prevent 5,033+ underpayments/year.  

3. **Reconcile compliance logic**:  
   - **Action**: Block payouts if `claim_status = 'Denied'` or `is_compliant = False`.  
   - **Impact**: Secure SAR 5.67M annually.  

4. **Target provider training**:  
   - **Action**: Coach providers P168, P853, P152 on coding "Over Limit" errors.  
   - **Impact**: Reduce 2,564 denials for billing errors.  

5. **Revise adjudicator workflows**:  
   - **Action**: Audit adjudicators approving non-compliant claims.  
   - **Impact**: Mitigate SAR 99.66M regulatory exposure.  

---

### **13. Future Work**  
- **Predictive modeling**: Build ML model to flag high-risk claims pre-adjudication.  
- **Provider network analysis**: Segment claims by provider tier (e.g., high vs. low compliance).  
- **SCHI benchmarking**: Compare cycle times against Saudi industry baselines.  

---

### **14. Technical Details**  
- **Tools**: Python (pandas, Seaborn), SQL, Tableau.  
- **SQL Queries**: [Link to GitHub]  
- **Tableau Dashboard**: [Link to Dashboard]  
- **Why Python**: Efficient handling of 50k+ records; Seaborn for diagnostic heatmaps.  

---

### **15. Assumptions and Caveats**  
1. **Date assumptions**: Claims with `service_date` before 2022-06-05 were excluded (data quality threshold).  
2. **Dental claims**: All ADA-coded claims assumed valid — actual clinical validity not audited.  
3. **Compliance logic**: `is_compliant` based on internal rules; SCHI regulations not fully mapped.  
4. **Denial reasons**: "Invalid Code" = provider error (not system mapping issues).  

---  
**Prepared by**: Data Analytics Team  
**Date**: June 20, 2025
