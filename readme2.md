## 🚀 User Growth - Cashback Program Optimized  

When I first joined the team, I identified that the Cashback Program, which had been running for nearly a year, was underperforming compared to expectations.

**Observation:**
- Cashback Adoption Rate: ~20% of sellers (significantly low)
- Cashback GMV Coverage: ~40% of GMV
- Free Shipping Adoption Rate: ~80% of sellers
- Free Shipping GMV Coverage: ~90% of GMV

**Key Issues Identified:**
- Many sellers were not joining Cashback at the same rate as Free Shipping.
- Cashback visibility to buyers was lower compared to Free Shipping.
- Some sellers were unaware of program benefits or eligibility requirements.

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

---

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

### **📊 Aggregation & Analysis in Python**

Now that the dataset is clean, we can calculate:
- **Cashback adoption rate**
- **Cashback GMV coverage**
- **Trends over time**
- **Segment & category performance**

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
 
**Objective:**
- Increase **Cashback adoption rate** during peak campaigns
- Boost **Cashback GMV coverage**
- Improve **customer awareness** of Cashback offers
- Encourage **seller awareness** of Cashback benefits by linking it to visible sales uplift

To evaluate the impact, we compared **campaign periods before and after** the policy change.

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

** Result **

| Period          | Cashback GMV   | Total GMV      | GMV Coverage (%) |
|-----------------|----------------|----------------|------------------|
| Before Trial    | 52,000,000,000 | 130,000,000,000 | 40.0             |
| During Trial    | 91,000,000,000 | 130,000,000,000 | 70.0             |
| After Trial     | 78,000,000,000 | 130,000,000,000 | 60.0             |


** Highlights: **
- Expected GMV coverage uplift: 20%
- Actual result of GMV coverage: 65-70%
- Sustained around 55-60% after the trial
