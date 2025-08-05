# 📂 Projects List

---

## 🚀 AI Trainer - Enhanced Chatbot Capability (Package Loss Case)  

<details>
<summary><b>📌 Summary</b></summary>  

**Overview:**  
- Identified package loss issue due to recipients being unavailable.  
- Accounted for ~18–20% of chatbot queries (Top 5 operational issue).  

**Solution:**  
- Added feature: Chatbot provides courier’s phone number during delivery.  
- Enabled customers to coordinate directly for successful delivery.  

**Impact:**  
- Package loss queries reduced from ~19% ➜ ~12% within 1 week of launch.  

</details>  

<details>
<summary><b>📂 Open Project Details</b></summary>  

### **📌 Background**  
A recurring issue of package loss during courier delivery was identified, primarily due to recipients being unavailable to receive their packages.  
- **Duration:** 2 weeks  
- **Severity:** Top 5 operational issue  
- **Impact:** ~18–20% of total chatbot queries  

---

### **💡 Solution**  
Implemented a chatbot enhancement feature that automatically provides customers with the courier’s phone number during delivery, enabling them to coordinate directly and ensure successful package receipt.

---

### **🔄 Process Flow**  

**Previous Flow:**  

Customer asks about package ➜
Chatbot checks delivery status ➜
If not delivered → Ask customer to wait for ETA ➜
If delivered → Ask customer to check with family/neighbors/receptionist ➜
If still not found → Escalate to live agent

![Previous Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/AIT%20Package%20Loss.jpg)  

---

**Updated Flow:**  

Customer asks about package ➜
Chatbot checks delivery status ➜
If not delivered → Ask customer to wait for ETA ➜
If delivered → Ask if courier has the package ➜
If yes → Provide courier’s phone number to customer

![Updated Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/AIT%20Package%20Loss%20NEW.jpg)  

---

### **📊 Supporting Data**

**Source Table:** `customers_queries`

| Column Name              | Data Type |
|--------------------------|-----------|
| customer_id              | VARCHAR   |
| customer_name            | VARCHAR   |
| customer_phone           | VARCHAR   |
| customer_address         | VARCHAR   |
| customer_zip_code        | VARCHAR   |
| ticket_id                | INT       |
| contact_reason_level_1   | VARCHAR   |
| contact_reason_level_2   | VARCHAR   |
| contact_reason_level_3   | VARCHAR   |
| ticket_submitted_date    | DATE      |
| ticket_due_date          | DATE      |
| is_case_resolved         | CHAR      |

---
### **📝 Sample Data**
| customer_id | customer_name   | customer_phone | customer_address       | customer_zip_code | ticket_id | contact_reason_level_1 | contact_reason_level_2 | contact_reason_level_3                     | ticket_submitted_date | ticket_due_date | is_case_resolved |
|-------------|----------------|----------------|------------------------|-------------------|-----------|------------------------|------------------------|--------------------------------------------|-----------------------|-----------------|------------------|
| CUS-001A    | John Doe       | 081234567890   | Jakarta Selatan        | 12190             | 1001      | Package related        | Package loss           | Status delivered but not received          | 2021-04-21            | 2021-04-28      | N                |
| CUS-002B    | Maria Sari     | 081298765432   | Bandung                | 40115             | 1002      | Payment related        | Refund request         | Payment deducted but order canceled        | 2021-04-22            | 2021-04-29      | Y                |
| CUS-003C    | Ahmad Fauzi    | 081311122233   | Surabaya               | 60234             | 1003      | Package related        | Damaged package        | Item broken upon delivery                   | 2021-04-23            | 2021-04-30      | Y                |
| CUS-004D    | Lisa Wong      | 081344455566   | Medan                  | 20151             | 1004      | Account related        | Login issue            | Forgot password                            | 2021-04-24            | 2021-05-01      | N                |
| CUS-005E    | Budi Santoso   | 081377788899   | Jakarta Barat          | 11460             | 1005      | Package related        | Package loss           | Status delivered but not received          | 2021-04-25            | 2021-05-02      | Y                |
---

### **📌 Pre‑Enhancement Query (30 days)**
```sql
SELECT 
    COUNT(*) AS total_queries,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'package loss' THEN 1 ELSE 0 END) AS package_loss_queries,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'package loss' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS package_loss_percentage
FROM customer_queries
WHERE ticket_submitted_date BETWEEN DATE '2021-04-20' AND DATE '2021-05-20';
```
**Result:**
| total_queries | package_loss_queries | package_loss_percentage |
|---------------|----------------------|--------------------------|
| 1034          | 198                  | 19.147886822529         |

---

### **📌 Post‑Enhancement Query (Using CTE)**
```sql
WITH package_loss_stats AS (
    SELECT 
        CASE 
            WHEN ticket_submitted_date BETWEEN DATE '2021-04-20' AND DATE '2021-05-20' 
                THEN 'Before Enhancement (30 days)'
            WHEN ticket_submitted_date BETWEEN DATE '2021-05-21' AND DATE '2021-05-27' 
                THEN 'After Enhancement (7 days)'
        END AS period,
        contact_reason_level_2
    FROM customer_queries
    WHERE ticket_submitted_date BETWEEN DATE '2021-04-20' AND DATE '2021-05-27'
)

SELECT 
    period,
    COUNT(*) AS total_queries,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'package loss' THEN 1 ELSE 0 END) AS package_loss_queries,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'package loss' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS package_loss_percentage
FROM package_loss_stats
GROUP BY period;
```

**Result:**
| Period                    | Total Queries | Package Loss Queries | Package Loss %     |
|---------------------------|---------------|----------------------|--------------------|
| Before Enhancement (30d)  | 1034          | 198                  | 19.14              |
| After Enhancement (7d)    | 980           | 118                  | 12.04              |

---

### **📉 Impact Visualization**
```
Package Loss Rate (%)
Before Enhancement: ████████████░░░░░░░ 19.14%
After Enhancement : ██████░░░░░░░░░░░░░ 12.04%
```

**📈 Summary:** After running the enhancement for a week, package loss queries dropped from ~19% to ~12%. 

</details>  

---

## 🚀 AI Trainer - Enhanced Chatbot Capability (Over SLA Shipping Case)  

<details>
<summary><b>📌 Summary</b></summary>  

**Overview:**  
- Identified a high volume of complaints due to **Over SLA orders** (seller took too long to process the order: SLA 7 Days).
- Implemented feature: **Chatbot allows cancellation after ≥7 days** (even if package is with courier) + **doorstep rejection**.  

**Impact (30 days):**  
- Over SLA complaints reduced from ~15% ➜ ~8%. 
- Negative reviews mentioning “late delivery” dropped significantly.

</details>  

<details>
<summary><b>📂 Open Project Details</b></summary>  

### **📌 Background**  
A recurring issue of **Over SLA orders** was identified:  
- **Order Flow (Before Enhancement):**

![Previous Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/Initial%20Order%20Flow.jpg)

- **Issues:**  
  - If seller takes full 7 days, customer's wait time can exceed **10 days**.  
  - Customers **cannot cancel** after courier handover, even if order is late.  
  - Result: **Negative reviews & CSAT drop**.

---

### **💡 Solution**  
- **Initial proposal:** Reduce seller SLA 7 ➜ 3 days (**Stakeholder can't fulfill**).
- **Implemented enhancement:**  
  - If order **in process ≥7 days**, chatbot offers **cancellation** (even after courier handover).  
  - Customers can **reject package at doorstep** (if the package is over the estimated SLA) → package returned automatically.  

---

### **🔄 Process Flow**  

**Previous Flow:**  

![Previous Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/Initial%20Order%20Flow.jpg)

**Updated Flow:**  

![Updated Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/Implemented%20new%20SLA.jpg)

Customer is given the option to wait until the estimated SLA or cancel the order and reject the package at doorstep.

---

### **📊 Supporting Data**  

**Source Table:** `customer_queries`  

| Column Name              | Data Type |
|--------------------------|-----------|
| customer_id              | VARCHAR   |
| customer_name            | VARCHAR   |
| customer_phone           | VARCHAR   |
| customer_address         | VARCHAR   |
| customer_zip_code        | VARCHAR   |
| ticket_id                | INT       |
| contact_reason_level_1   | VARCHAR   |
| contact_reason_level_2   | VARCHAR   |
| contact_reason_level_3   | VARCHAR   |
| ticket_submitted_date    | DATE      |
| ticket_due_date          | DATE      |
| is_case_resolved         | CHAR      |

---

### **📝 Sample Data**
| customer\_id | customer\_name | customer\_phone | customer\_address | customer\_zip\_code | ticket\_id | contact\_reason\_level\_1 | contact\_reason\_level\_2 | contact\_reason\_level\_3                    | ticket\_submitted\_date | ticket\_due\_date | is\_case\_resolved |
| ------------ | -------------- | --------------- | ----------------- | ------------------- | ---------- | ------------------------- | ------------------------- | -------------------------------------------- | ----------------------- | ----------------- | ------------------ |
| CUS-010A     | Andi Wijaya    | 081234567801    | Jakarta Selatan   | 12190               | 2001       | Order related             | Over SLA                  | Order processing > 7 days, still in transit  | 2021-05-01              | 2021-05-15        | N                  |
| CUS-011B     | Siti Rahma     | 081298765802    | Bandung           | 40115               | 2002       | Order related             | Over SLA                  | Package stuck with courier > SLA             | 2021-05-03              | 2021-05-17        | Y                  |
| CUS-012C     | Budi Santoso   | 081311122803    | Surabaya          | 60234               | 2003       | Order related             | Over SLA                  | Cannot cancel after SLA breach               | 2021-05-04              | 2021-05-18        | Y                  |
| CUS-013D     | Lina Kartika   | 081344455804    | Medan             | 20151               | 2004       | Order related             | Over SLA                  | Late package, cancellation requested at door | 2021-05-06              | 2021-05-20        | Y                  |
| CUS-014E     | Agus Pratama   | 081377788805    | Jakarta Barat     | 11460               | 2005       | Order related             | Over SLA                  | Delivery delay, customer refused at doorstep | 2021-05-07              | 2021-05-21        | Y                  |


---

### **📌 Pre‑Enhancement Query (30 days)**  
```sql
SELECT 
    COUNT(*) AS total_complaints,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'over sla' THEN 1 ELSE 0 END) AS over_sla_complaints,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'over sla' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS over_sla_percentage
FROM customer_complaints
WHERE ticket_submitted_date BETWEEN DATE '2021-04-01' AND DATE '2021-04-30';
```  

**Result:**  
| total_complaints | over_sla_complaints | over_sla_percentage |
|------------------|---------------------|---------------------|
| 980              | 147                 | 15.0                |

---

### **📌 Post‑Enhancement Query (30 days after launch)**  
```sql
WITH sla_stats AS (
    SELECT 
        CASE 
            WHEN ticket_submitted_date BETWEEN DATE '2021-04-01' AND DATE '2021-04-30' 
                THEN 'Before Enhancement'
            WHEN ticket_submitted_date BETWEEN DATE '2021-05-01' AND DATE '2021-05-30' 
                THEN 'After Enhancement'
        END AS period,
        contact_reason_level_2
    FROM customer_complaints
    WHERE ticket_submitted_date BETWEEN DATE '2021-04-01' AND DATE '2021-05-30'
)

SELECT 
    period,
    COUNT(*) AS total_complaints,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'over sla' THEN 1 ELSE 0 END) AS over_sla_complaints,
    SUM(CASE WHEN LOWER(contact_reason_level_2) = 'over sla' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS over_sla_percentage
FROM sla_stats
GROUP BY period;
```  

**Result:**  
| Period                | Total Complaints | Over SLA Complaints | Over SLA % |
|-----------------------|------------------|---------------------|------------|
| Before Enhancement    | 980              | 147                 | 15.0       |
| After Enhancement     | 1020             | 82                  | 8.0        |

---

### **📉 Impact Visualization**  
```
Over SLA Complaint Rate (%)
Before Enhancement: ██████████░░░░░░░░░ 15.0%
After Enhancement : ██████░░░░░░░░░░░░░ 8.0%
```  

**📈 Summary:** After running the enhancement for 30 days, **Over SLA complaints dropped from ~15% to ~8%** and **CSAT for SLA-related cases improved by +12 points** (estimated).

</details>  
