
# 📂 Projects  

<details>
<summary><b>2021 - 2022 | AI Trainer</b></summary>

## 🚀 Project: Enhanced Chatbot Capability - Loss Package During Delivery

---

### **📌 Background**
A recurring issue of package loss during courier delivery was identified, primarily due to recipients being unavailable to receive their packages.  
- **Duration:** Several weeks  
- **Severity:** Top 5 operational issue  
- **Impact:** ~18–20% of total chatbot queries  

---

### **💡 Solution**
Implemented a chatbot enhancement feature that automatically provides customers with the courier’s phone number during delivery, enabling them to coordinate directly and ensure successful package receipt.

---

### **🔄 Process Flow**

**Previous Flow:**
```
Customer asks about package ➜  
Chatbot checks delivery status ➜  
If not delivered → Ask customer to wait for ETA ➜  
If delivered → Ask customer to check with family/neighbors/receptionist ➜  
If still not found → Escalate to live agent
```
![Previous Flow](https://github.com/adhaafriza/afriza-s_portfolio/blob/main/AIT%20Package%20Loss.jpg)

---

**Updated Flow:**
```
Customer asks about package ➜  
Chatbot checks delivery status ➜  
If not delivered → Ask customer to wait for ETA ➜  
If delivered → Ask if courier has the package ➜  
If yes → Provide courier’s phone number to customer
```
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
