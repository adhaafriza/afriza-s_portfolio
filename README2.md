
# 📂 Projects Summary  

## 🚀 AI Trainer - Enhanced Chatbot Capability (Over SLA Shipping Case)  

**Overview:**  
- Identified a high volume of complaints due to **Over SLA orders** (seller took too long to process the order: SLA 7 Days).
- Implemented feature: **Chatbot allows cancellation after ≥7 days** (even if package is with courier) + **doorstep rejection**.  

**Impact (30 days):**  
- Over SLA complaints reduced from ~15% ➜ ~8%. 
- Negative reviews mentioning “late delivery” dropped significantly.

---

# 📂 Projects  

<details>
<summary><b>2021 - 2022 | AI Trainer</b></summary>  

## 🚀 Project: Enhanced Chatbot Capability - Over SLA Shipping Case  

---

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

**Source Table:** `customer_complaints`  

| Column Name              | Data Type |
|--------------------------|-----------|
| order_id                 | VARCHAR   |
| customer_id              | VARCHAR   |
| complaint_reason         | VARCHAR   |
| order_status             | VARCHAR   |
| processing_days          | INT       |
| is_courier_handover      | CHAR      |
| is_cancellation_allowed  | CHAR      |
| csat_score               | INT       |
| complaint_date           | DATE      |

---

### **📌 Pre‑Enhancement Query (30 days)**  
```sql
SELECT 
    COUNT(*) AS total_complaints,
    SUM(CASE WHEN LOWER(complaint_reason) LIKE '%over sla%' THEN 1 ELSE 0 END) AS over_sla_complaints,
    SUM(CASE WHEN LOWER(complaint_reason) LIKE '%over sla%' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS over_sla_percentage
FROM customer_complaints
WHERE complaint_date BETWEEN DATE '2021-04-01' AND DATE '2021-04-30';
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
            WHEN complaint_date BETWEEN DATE '2021-04-01' AND DATE '2021-04-30' 
                THEN 'Before Enhancement'
            WHEN complaint_date BETWEEN DATE '2021-05-01' AND DATE '2021-05-30' 
                THEN 'After Enhancement'
        END AS period,
        complaint_reason
    FROM customer_complaints
    WHERE complaint_date BETWEEN DATE '2021-04-01' AND DATE '2021-05-30'
)

SELECT 
    period,
    COUNT(*) AS total_complaints,
    SUM(CASE WHEN LOWER(complaint_reason) LIKE '%over sla%' THEN 1 ELSE 0 END) AS over_sla_complaints,
    SUM(CASE WHEN LOWER(complaint_reason) LIKE '%over sla%' THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS over_sla_percentage
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

**📈 Summary:** After running the enhancement for 30 days, **Over SLA complaints dropped from ~15% to ~8%** and **CSAT for SLA-related cases improved by +12 points**.

</details>  
