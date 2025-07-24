# Enhancing-Brand-Reputation-with-PostgreSQL

## Introduction  
🚀 *Dive into brand reputation analytics!*  
This project focuses on **social media sentiment**, **customer complaints**, and **crisis management** to help AfriTech Electronics Ltd. monitor its public image in real-time. Using SQL and Power BI, I built a structured data pipeline and interactive dashboards that transform raw customer and social media data into **actionable insights**.

🔍 **SQL queries?** They’re in the `project_sql` folder.

---

## Background  
Brands live or die by their reputation in the digital era. Negative buzz, product recalls, and slow crisis responses can crush customer trust and sales.  
This project simulates how **data analysts can proactively monitor and protect a brand** using:  
- **Customer and sales trends**  
- **Sentiment analysis of social media**  
- **Complaint and crisis resolution metrics**  

---

## Key Questions Answered with SQL  
- Which products are driving the most revenue?  
- What regions have the highest complaint rates?  
- How does sentiment trend across social media platforms?  
- Which crisis events had the longest resolution times?  
- How can we track competitor mentions vs. our brand?

---

## Tools I Used  
- **SQL (PostgreSQL):** For advanced querying, joins, and aggregations.  
- **Power BI:** For building interactive dashboards with KPIs, sentiment trends, and drill-through features.  
- **Visual Studio Code:** To manage SQL scripts and data preprocessing.  
- **Git & GitHub:** For version control and collaboration.

---

## The SQL Analysis  

### 1. Customer & Sales Insights  
*Query to calculate total revenue, sales by product, and customer segmentation by region and income.*  
```sql
SELECT region, product_purchased, SUM(purchase_amount) AS total_sales
FROM transactions
GROUP BY region, product_purchased
ORDER BY total_sales DESC;
```

### 2. Sentiment Analysis  
*Query to track positive vs. negative sentiment across platforms.*  
```sql
SELECT platform, sentiment, COUNT(*) AS sentiment_count
FROM social_media
GROUP BY platform, sentiment
ORDER BY sentiment_count DESC;
```

### 3. Crisis & Complaints Trends  
*Query to measure crisis resolution time and track complaint patterns.*  
```sql
SELECT product_purchased, AVG(first_response_time - crisis_event_time) AS avg_resolution_time
FROM social_media
WHERE resolution_status = TRUE
GROUP BY product_purchased;
```

*(Additional queries for competitor mentions, NPS scoring, and influencer impact are in `project_sql`.)*

---

## What I Learned  
- **Advanced SQL:** Joins, CTEs, and window functions to track sentiment trends and KPIs.  
- **ETL Workflows:** Data cleaning and transformation using SQL before loading into Power BI.  
- **Dashboard Design:** Creating interactive, stakeholder-friendly views to make data stories clear.

---

## Closing Thoughts  
This project mirrors the real-world role of a data analyst in **brand monitoring and crisis prevention**. By combining SQL analytics with Power BI storytelling, I created a 360° view of customer perception and market trends.

-
