# 📂 Projects List

---

## 🚀 User Growth - Cashback Program Optimized

<details>
<summary><b>📌 Summary</b></summary>  

**Overview:**  
- Cashback program underperformed compared to Free Shipping:  
  - Cashback adoption ~20%  
  - Cashback GMV coverage ~40% vs Free Shipping ~90%  
- Low seller participation & awareness were key barriers.  

---

**Solution:**  
- **Initiative 1:** Mandatory Cashback during Double Date & Payday campaigns to drive short-term adoption.  
- **Initiative 2:** Free Trial Cashback for Top 500 high-performing sellers (High & High Medium segments) to drive long-term adoption.  

---

**Impact:**  
- **During campaigns:** GMV coverage spiked to 70–75%, but reverted to ~40% post-campaign.  
- **During trial:** GMV coverage increased to 65–70% (exceeded expected +20%).  
- **After trial:** Sustained GMV coverage at 55–60% as many sellers remained in the program.

</details>  

<details>
<summary><b>📂 Open Project Details</b></summary>

**Observation:**
- Cashback Adoption Rate: ~20% of sellers (significantly low)
- Cashback GMV Coverage: ~40% of GMV
- Free Shipping Adoption Rate: ~80% of sellers
- Free Shipping GMV Coverage: ~90% of GMV

---

**Key Issues Identified:**
- Many sellers were not joining Cashback at the same rate as Free Shipping.
- Cashback visibility to buyers was lower compared to Free Shipping.
- Some sellers were unaware of program benefits or eligibility requirements.

---

**Impact:**
- Limited incremental sales contribution from Cashback program.
- Low Cashback redemption from the customer side.

---

### **📊 Supporting Data**

**Source Tables:**  

#### `seller_performance`  
| Column Name        | Data Type |
|--------------------|-----------|
| date               | DATE      |
| seller_id          | STRING    |
| seller_name        | STRING    |
| seller_segment     | STRING    |
| seller_category    | STRING    |
| total_order        | INT64     |
| total_unit_sold    | INT64     |
| gmv                | FLOAT64   |

#### `seller_program_details`  
| Column Name              | Data Type |
|--------------------------|-----------|
| seller_id                | STRING    |
| seller_name              | STRING    |
| seller_segment           | STRING    |
| seller_category          | STRING    |
| seller_join_date         | DATE      |
| seller_lifetime          | INT64     |
| is_join_free_shipping    | STRING    |
| is_join_cashback         | STRING    |
| is_holiday_mode          | STRING    |

---

### **📌 SQL Query**  
```sql
WITH seller_data AS (
    SELECT 
        sp.date,
        sp.seller_id,
        sp.seller_name,
        sp.seller_segment,
        sp.seller_category,
        sp.total_order,
        sp.total_unit_sold,
        sp.gmv,
        pd.seller_join_date,
        pd.seller_lifetime,
        pd.is_join_free_shipping,
        pd.is_join_cashback,
        pd.is_holiday_mode
    FROM `project.dataset.seller_performance` sp
    JOIN `project.dataset.seller_program_details` pd
        ON sp.seller_id = pd.seller_id
    WHERE sp.date >= DATE_SUB(CURRENT_DATE(), INTERVAL 3 MONTH)
)
SELECT *
FROM seller_data;
```

### **📝 Sample Extracted Data (10 Randomized Rows)**  

| date       | seller_id | seller_name         | seller_segment     | seller_category   | total_order | total_unit_sold | gmv       | seller_join_date | seller_lifetime | is_join_free_shipping | is_join_cashback | is_holiday_mode |
|------------|-----------|--------------------|--------------------|-------------------|-------------|-----------------|-----------|------------------|-----------------|-----------------------|------------------|-----------------|
| 2025-05-01 | S001      | PT Elektronik      | High Seller        | ELECTRONIC        | 230         | 400             | 4500000   | 2022-01-05       | 500             | Y                     | Yes              | N               |
| 2025-05-01 | S002      | Fashion Mart       | Medium Seller      | fashion           | 120         | 220             | 2100000   | 2021-12-11       | 570             | y                     | n                | N               |
| 2025-05-02 | S003      | FMCG Center        | Low Seller         | fmcg              | 45          | 90              | 450000    | 2023-03-18       | 150             | No                    | YES              | N               |
| 2025-05-02 | S004      | Food & Drink Co.   | High Medium Seller | F&B               | 80          | 150             | 1200000   | 2022-10-25       | 300             | Y                     | y                | Y               |
| 2025-05-03 | S005      | Beauty Care Ltd    | Medium Seller      | Personal care     | 65          | 110             | 890000    | 2021-08-12       | 650             | YES                   | NO               | N               |
| 2025-05-03 | S006      |  PT Elektronik     | High Seller        | electronic        | 300         | 500             | 5500000   | 2022-01-05       | 500             | y                     | y                | N               |
| 2025-05-04 | S007      | FashionMart        | Medium Seller      | Fashion           | 140         | 230             | 2600000   | 2022-04-01       | 400             | n                     | N                | N               |
| 2025-05-04 | S008      | Food & Drink Co    | High Medium Seller | f&b               | 90          | 170             | 1300000   | 2022-10-25       | 300             | Yes                   | Yes              | Y               |
| 2025-05-05 | S009      | Beauty Care LTD    | Medium Seller      | PERSONAL CARE     | 70          | 120             | 940000    | 2021-08-12       | 650             | y                     | no               | N               |
| 2025-05-05 | S010      | FMCG center        | Low Seller         | FMCG              | 55          | 100             | 500000    | 2023-03-18       | 150             | N                     | y                | N               |

Result showed some inconsistent writing of the data.

### **🧹 Data Cleaning in Python**

The extracted dataset contains several inconsistencies that need to be cleaned before analysis:
- **Category names** are inconsistent (`ELECTRONIC`, `electronic`, `Electronics`).
- **Program flags** are mixed (`y`, `Y`, `Yes`, `YES`).
- **Seller names** contain extra spaces and inconsistent casing.
- **Missing GMV / orders** for some sellers.
- **Holiday mode** not standardized (`Y/N` mix).

```python
import pandas as pd

# Load CSV exported from SQL
df = pd.read_csv("cashback_free_shipping_data.csv")

# 1️⃣ Clean seller_category text
df['seller_category'] = df['seller_category'].str.strip().str.lower().replace({
    'electronics': 'electronic',
    'electronic': 'electronic',
    'ELECTRONIC': 'electronic',
    'fashion': 'fashion',
    'FASHION': 'fashion',
    'fmcg': 'fmcg',
    'FMCG': 'fmcg',
    'f&b': 'f&b',
    'F&B': 'f&b',
    'personal care': 'personal care',
    'PERSONAL CARE': 'personal care'
})

# 2️⃣ Standardize program flags
df['is_join_cashback'] = df['is_join_cashback'].astype(str).str.strip().str.upper().replace({
    'YES': 'Y', 'NO': 'N', 'Y': 'Y', 'N': 'N'
})
df['is_join_free_shipping'] = df['is_join_free_shipping'].astype(str).str.strip().str.upper().replace({
    'YES': 'Y', 'NO': 'N', 'Y': 'Y', 'N': 'N'
})

# 3️⃣ Clean seller_name (remove extra spaces, unify casing)
df['seller_name'] = df['seller_name'].str.strip().str.title()

# 4️⃣ Fill missing numerical values
df[['gmv','total_order','total_unit_sold']] = df[['gmv','total_order','total_unit_sold']].fillna(0)

# Check cleaned data
print(df.head(10))
```

---

#### **1️⃣ Cashback Adoption Rate & GMV Coverage**
```python
# Calculate adoption rate
adoption_rate = (
    df.groupby('is_join_cashback')['seller_id']
      .nunique()
      .div(df['seller_id'].nunique())
      .mul(100)
      .round(1)
)

# Calculate GMV coverage
gmv_coverage = (
    df.groupby('is_join_cashback')['gmv']
      .sum()
      .div(df['gmv'].sum())
      .mul(100)
      .round(1)
)

# Combine into one DataFrame
summary_df = pd.DataFrame({
    'Adoption Rate (%)': adoption_rate,
    'GMV Coverage (%)': gmv_coverage
}).reset_index().rename(columns={'is_join_cashback':'Cashback Participation'})

print(summary_df)
```

### **📌 Cashback Adoption & GMV Coverave Rate (%)**
| Cashback Participation | Adoption Rate (%) | GMV Coverage (%) |
|------------------------|-------------------|------------------|
| No (N)                 | 78.5              | 60.2             |
| Yes (Y)                | 21.5              | 39.8             |

---

### **📌 Additional Enhancement: Mandatory Cashback During Campaign Period**

The Cashback program had not been contributing significantly to GMV or seller adoption rates.  
To address this, we implemented a **policy change**:

**Mandatory Cashback Enrollment** for sellers participating in major campaigns:
  - **Double Date Campaigns** (e.g., 4.4, 5.5, 6.6) — 3-day events
  - **Payday Campaigns** (e.g., 25–30/31 each month)

---
 
**Objective:**
- Increase **Cashback adoption rate** during peak campaigns
- Boost **Cashback GMV coverage**
- Improve **customer awareness** of Cashback offers
- Encourage **seller awareness** of Cashback benefits by linking it to visible sales uplift

To evaluate the impact, we compared **campaign periods before and after** the policy change.

---

### **📊 Supporting Data**

**Table source will be the same**

### **📌 SQL Query - Extract the Data**

```
WITH campaign_data AS (
    SELECT 
        sp.date,
        sp.seller_id,
        sp.gmv,
        pd.is_join_cashback,
        CASE 
            WHEN EXTRACT(DAY FROM sp.date) IN (4,5,6) THEN 'Double Date'
            WHEN EXTRACT(DAY FROM sp.date) BETWEEN 25 AND 31 THEN 'Payday'
        END AS campaign_type,
        CASE 
            WHEN sp.date < DATE('2025-05-01') THEN 'Before Mandatory'
            ELSE 'After Mandatory'
        END AS policy_period
    FROM `project.dataset.seller_performance` sp
    JOIN `project.dataset.seller_program_details` pd
        ON sp.seller_id = pd.seller_id
    WHERE (
        EXTRACT(DAY FROM sp.date) IN (4,5,6)
        OR EXTRACT(DAY FROM sp.date) BETWEEN 25 AND 31
    )
)
SELECT *
FROM campaign_data
ORDER BY policy_period, campaign_type, date;
```

---

### **📌 Python Analysis**

```
import pandas as pd
import matplotlib.pyplot as plt

# Load campaign CSV
campaign_df = pd.read_csv("campaign_data.csv")

# Clean flags
campaign_df['is_join_cashback'] = campaign_df['is_join_cashback'].str.upper().map({'Y': 'Y', 'YES': 'Y', 'N': 'N', 'NO': 'N'})

# Correct calculation for GMV coverage
summary = campaign_df.groupby(['policy_period','campaign_type']).apply(
    lambda g: pd.Series({
        'total_sellers': g['seller_id'].nunique(),
        'adoption_rate': (g['is_join_cashback'].eq('Y').sum() / g['seller_id'].nunique() * 100).round(1),
        'gmv_coverage': (g.loc[g['is_join_cashback'] == 'Y', 'gmv'].sum() / g['gmv'].sum() * 100).round(1)
    })
).reset_index()

print(summary)

```

### 📊 Campaign Impact: Adoption Rate & GMV Coverage Before vs After Mandatory Cashback

| Policy Period     | Campaign Type | Adoption Rate (%) | GMV Coverage (%) |
|-------------------|---------------|-------------------|------------------|
| Before Mandatory  | Double Date   | 23.1              | 41.2             |
| Before Mandatory  | Payday        | 21.8              | 39.5             |
| After Mandatory   | Double Date   | 59.3              | 75.8             |
| After Mandatory   | Payday        | 52.0              | 68.4             |

**⚠️ Some Issue Occurred**

While making Cashback mandatory during campaign periods successfully increased adoption rate and GMV coverage, the improvement was temporary.  
Once the campaign ended, both metrics returned close to their initial levels.

---

### 💡 Implemented New Initiative: Free Trial Cashback Program

** Cashback Program Free Trial **
- Duration: 3 months
- Criteria: High & High Medium Seller, GMV > Rp30,000,000, Order >= 45 in the past 1 month, never joined cashback program in the last 3 months

---

### **📊 Supporting Data**

**Source Tables:**  

#### `seller_performance`
| Column Name        | Data Type |
|--------------------|-----------|
| date               | DATE      |
| seller_id          | STRING    |
| seller_name        | STRING    |
| seller_segment     | STRING    |
| seller_category    | STRING    |
| total_order        | INT64     |
| total_unit_sold    | INT64     |
| gmv                | FLOAT64   |
| is_join_cashback   | STRING    |

---

#### `seller_performance_order_level`
| Column Name        | Data Type |
|--------------------|-----------|
| date               | DATE      |
| seller_id          | STRING    |
| seller_name        | STRING    |
| seller_segment     | STRING    |
| seller_category    | STRING    |
| product_name       | STRING    |
| product_category   | STRING    |
| order_id           | STRING    |
| unit_sold          | INT64     |
| gmv                | FLOAT64   |

---

### **📌 SQL Query - Extract the Data**

```
WITH trial_sellers AS (
    SELECT 
        seller_id,
        seller_name,
        seller_segment,
        seller_category,
        gmv
    FROM `project.dataset.seller_performance`
    WHERE gmv > 30000000
      AND total_order > 45
      AND is_join_cashback = 'N'
      AND seller_segment IN ('High Seller', 'High Medium Seller')
    ORDER BY gmv DESC
    LIMIT 500
),

order_gmv AS (
    SELECT 
        o.seller_id,
        o.order_id,
        SUM(o.gmv) AS order_total_gmv
    FROM `project.dataset.seller_performance_order_level` o
    JOIN trial_sellers t
        ON o.seller_id = t.seller_id
    GROUP BY o.seller_id, o.order_id
),

estimated_cost AS (
    SELECT 
        seller_id,
        order_id,
        order_total_gmv,
        LEAST(order_total_gmv * 0.10, 20000) * 0.45 AS estimated_cashback_cost
    FROM order_gmv
),

segment_breakdown AS (
    SELECT 
        t.seller_segment,
        COUNT(DISTINCT a.seller_id) AS total_sellers,
        SUM(a.order_total_gmv) AS total_gmv,
        SUM(a.estimated_cashback_cost) AS estimated_cashback_cost
    FROM estimated_cost a
    JOIN trial_sellers t
        ON a.seller_id = t.seller_id
    GROUP BY t.seller_segment
),

platform_gmv AS (
    SELECT 
        SUM(gmv) AS total_platform_gmv
    FROM `project.dataset.seller_performance`
)

SELECT 
    sb.seller_segment,
    sb.total_sellers,
    sb.total_gmv,
    sb.estimated_cashback_cost,
    p.total_platform_gmv,
    ROUND(sb.total_gmv / p.total_platform_gmv * 100, 2) AS gmv_coverage_percentage
FROM segment_breakdown sb
CROSS JOIN platform_gmv p

UNION ALL

SELECT 
    'TOTAL' AS seller_segment,
    SUM(sb.total_sellers),
    SUM(sb.total_gmv),
    SUM(sb.estimated_cashback_cost),
    p.total_platform_gmv,
    ROUND(SUM(sb.total_gmv) / p.total_platform_gmv * 100, 2) AS gmv_coverage_percentage
FROM segment_breakdown sb
CROSS JOIN platform_gmv p;

```
**Result:**

| Seller Segment       | Total Sellers | Total GMV       | Estimated Cashback Cost | Platform GMV      | GMV Coverage (%) |
|----------------------|---------------|-----------------|-------------------------|-------------------|------------------|
| High Seller          | 350           | 20,800,000,000  | 210,000,000             | 130,000,000,000  | 16.00            |
| High Medium Seller   | 150           | 5,200,000,000   | 55,000,000              | 130,000,000,000  | 4.00             |
| TOTAL                | 500           | 26,000,000,000  | 265,000,000             | 130,000,000,000  | 20.00            |

**Notes:**
- The adoption rate won't increased a lot, but the GMV coverage estimated of 20% increase.

---

### **📌 Python Scenario Simulation**

import pandas as pd
```
# Current baseline data from SQL
data = {
    'Seller Segment': ['High Seller', 'High Medium Seller'],
    'Total GMV': [20_800_000_000, 5_200_000_000],
    'Estimated Cashback Cost (10% cap 20k)': [210_000_000, 55_000_000]
}

df = pd.DataFrame(data)

# Simulation parameters
cashback_rate_new = 0.15
cashback_cap_new = 25000
redemption_rate = 0.45

# Simulated cost with new parameters
df['Estimated Cashback Cost (15% cap 25k)'] = df.apply(
    lambda row: min(row['Total GMV'] * cashback_rate_new, row['Total GMV']/row['Total GMV']*cashback_cap_new*1000) * redemption_rate,
    axis=1
)

df.loc['TOTAL'] = df[['Estimated Cashback Cost (10% cap 20k)', 'Estimated Cashback Cost (15% cap 25k)']].sum()
```

**Result:**
| Seller Segment       | Cashback Cost (10% cap 20k) | Cashback Cost (15% cap 25k) |
|----------------------|-----------------------------|-----------------------------|
| High Seller          | 210,000,000                 | 315,000,000                 |
| High Medium Seller   | 55,000,000                  | 82,500,000                  |
| **TOTAL**            | 265,000,000                 | 397,500,000                 |

---

### **📌 Evaluating the Result**

```
WITH cashback_gmv AS (
    SELECT 
        CASE 
            WHEN DATE(date) BETWEEN '2025-02-01' AND '2025-04-30' THEN 'During Trial'
            WHEN DATE(date) < '2025-02-01' THEN 'Before Trial'
            ELSE 'After Trial'
        END AS period,
        SUM(CASE WHEN is_join_cashback = 'Y' THEN gmv ELSE 0 END) AS cashback_gmv,
        SUM(gmv) AS total_gmv
    FROM `project.dataset.seller_performance`
    GROUP BY period
)

SELECT 
    period,
    cashback_gmv,
    total_gmv,
    ROUND(cashback_gmv / total_gmv * 100, 2) AS gmv_coverage_percentage
FROM cashback_gmv
ORDER BY 
    CASE period
        WHEN 'Before Trial' THEN 1
        WHEN 'During Trial' THEN 2
        WHEN 'After Trial' THEN 3
    END;
```

**Result**

| Period          | Cashback GMV   | Total GMV      | GMV Coverage (%) |
|-----------------|----------------|----------------|------------------|
| Before Trial    | 52,000,000,000 | 130,000,000,000 | 40.0             |
| During Trial    | 91,000,000,000 | 130,000,000,000 | 70.0             |
| After Trial     | 78,000,000,000 | 130,000,000,000 | 60.0             |


**Highlights:**
- Expected GMV coverage uplift: 20%
- Actual result of GMV coverage: 65-70%
- Sustained around 55-60% after the trial

---

## **Final Summary of Initiatives**

| Initiative                     | Expected Result           | Actual Result              | Lesson Learned                  |
|--------------------------------|--------------------------|----------------------------|----------------------------------|
| Mandatory Campaign Cashback    | +15–20% coverage spike   | +30–35% spike (temporary) | Campaign boosts are short-term   |
| Free Trial Cashback (500 Sellers) | +20% coverage increase | +25–30% increase sustained at 55–60% | Targeted trial drives retention |


</details>

---

## 🚀 AI Trainer - Enhanced Chatbot Capability (Package Loss Case)  

<details>
<summary><b>📌 Summary</b></summary>  

**Overview:**  
- Identified package loss issue due to recipients being unavailable.  
- Accounted for ~18–20% of chatbot queries (Top 5 operational issue).

---

**Solution:**  
- Added feature: Chatbot provides courier’s phone number during delivery.  
- Enabled customers to coordinate directly for successful delivery.

---

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

--- 

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
