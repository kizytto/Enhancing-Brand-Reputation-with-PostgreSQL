# Enhancing-Brand-Reputation-with-PostgreSQL

## Introduction  
🚀 *Dive into brand reputation analytics!*  
This project focuses on **social media sentiment**, **customer complaints**, and **crisis management** to help AfriTech Electronics Ltd. monitor its public image in real-time. Using SQL and Power BI, I built a structured data pipeline and interactive dashboards that transform raw customer and social media data into **actionable insights**.

🔍 **SQL queries?** 
# The Analysis
Each query for this project aimed at investigating specific aspects of the brand reputation analytics. Here’s how I approached each question:
 ```sql
CREATE TABLE Afritech (
CustomerID INT, 
CustormerName TEXT,
Region TEXT, 
Age INT, 
Income Numeric(10, 2),
CustomerType TEXT, 
TransactionYear INT,
TransactionDate DATE, 
ProductPurchased TEXT,
PurchaseAmount Numeric(10,2), 
ProductRecalled BOOLEAN,
Competitor_x TEXT, 
InteractionDate DATE,
Plaform Text, 
PostType Text, 
EngagementLikes INT,
EngagementShares INT, 
EngagementComments INT,
UserFollowers INT, 
InfluencerScore Numeric(10, 2),
BrandMention BOOLEAN, 
CompetitorMention Boolean,
Sentiment Text, 
CrisiEventTime DATE,
FirstResponseTime DATE, 
ResolutionStatus BOOLEAN,
NPSResponse int
);
SELECT * FROM Afritech Limit 50

CREATE TABLE CustomerData( 
CustomerID INT Primary key,
CustomerName VARCHAR(50),
Region text,
Age INT,
INCOME Numeric(10, 2),
Customertype VARCHAR(20)
);

CREATE TABLE TransactionData(
TransactionID SERIAL Primary key,
CustomerID INT,
TransactionYear Varchar (4),
TransactionDate DATE,
ProductPurchased Varchar(255),
PurchaseAmount Numeric(10, 2),
ProductRecalled BOOLEAN,
competitor Varchar(255),
FOREIGN KEY (CustomerID) 
References Customerdata
);

CREATE TABLE SocialMedia(
PostID SERIAL Primary Key,
CustomerID INT,
Interactiondate DATE,
Platform VARCHAR (50),
PostType VARCHAR (20),
Engagementlikes int,
Engagementshares int,
Engagementcomments int,
UserFollowers int,
InfluencerScore Numeric(10, 2),
BrandMention Boolean,
CompetitorMention Boolean,
Sentiment Varchar (50),
competitor_x VARCHAR(255),
CrisisEventtime DATE,
FirstResponse Date,
ResolutionStatus BOOLEAN,
NPSResponse int,
FOREIGN KEY (CustomerID) 
References CustomerData
);

DROP TABLE SocialMedia;

INSERT INTO CustomerData(CustomerID, CustomerName, Age, Region, Income, CustomerType)  
SELECT DISTINCT CustomerID, CustormerName, Age, Region, Income, CustomerType 
FROM afritech; 

SELECT * FROM CUSTOMERDATA;


INSERT INTO TransactionData(CustomerID, TransactionYear, TransactionDate, ProductPurchased, PurchaseAmount, ProductRecalled, Competitor) 
SELECT CustomerID, TransactionYear, TransactionDate, ProductPurchased, PurchaseAmount, ProductRecalled, Competitor_x  
FROM afritech  
WHERE TransactionDate IS NOT NULL; 


INSERT INTO SocialMedia(CustomerID, InteractionDate, Platform, EngagementLikes, EngagementShares, EngagementComments, 
UserFollowers, InfluencerScore, BrandMention, CompetitorMention, Sentiment, Competitor_x, CrisisEventTime, 
FirstResponse, ResolutionStatus, NPSResponse, posttype)  
SELECT CustomerID, InteractionDate, Plaform, EngagementLikes, EngagementShares, EngagementComments, UserFollowers, 
InfluencerScore, BrandMention, CompetitorMention, Sentiment, Competitor_x, CrisiEventTime, FirstResponseTime, 
ResolutionStatus, NPSResponse, posttype 
FROM afritech  
WHERE InteractionDate IS NOT NULL; 

SELECT * FROM SocialMedia;

-- Data VALIDATION

SELECT * FROM SocialMedia;

SELECT * FROM TransactionData;

SELECT * FROM CUSTOMERDATA;


-- Data Cleaning (Missing Values) 

SELECT COUNT(*) 
FROM Customerdata 
WHERE CustomerID IS NULL; 

SELECT COUNT(*) 
FROM TransactionData 
WHERE TransactionID IS NULL; 

SELECT COUNT(*)  
FROM SocialMedia  
WHERE PostID IS NULL; 

-- EXPLORATORY DATA ANALYSIS 
-- Customer Data 
-- How many customers in each region 

SELECT REGION, Count(*) AS Number 
FROM CustomerData 
GROUP BY Region 
ORDER BY COUNT (*) DESC; 

-- How many unique customers do we have? 

SELECT COUNT(DISTINCT CustomerID) 
AS UniqueCustomers 
FROM Customerdata; 

-- What is the highest, lowest and average age of the customers? 
SELECT MAX(Age) AS highest_age,
MIN(Age) AS lowest_age, 
ROUND(AVG(Age), 0) AS Average_age 
FROM CustomerData;

-- Customer Type
SELECT customertype, COUNT (*) AS Type_Count
FROM customerdata
GROUP BY Customertype
ORDER BY Count (*) DESC;

-- TRANSACTION DATA

SELECT ROUND(MAX(Income), 2) AS highest_Income, 
ROUND(MIN(Income), 2) AS lowest_Income, 
ROUND(AVG(Income), 2) AS Average_Income 
FROM CustomerData; 

-- What is the product the product pricing distribution at afritech? 

SELECT MAX(PurchaseAmount) AS highest_price, 
MIN(PurchaseAmount) AS lowest_price, 
ROUND(SUM(PurchaseAmount) /COUNT (TransactionID),0) AS Average_price 
FROM TransactionData; 

-- What are the product purchased behavior? 

SELECT ProductPurchased, COUNT(*) AS PurchaseQuantity, SUM(PurchaseAmount) AS TotalSales 
FROM TransactionData 
GROUP BY ProductPurchased 
ORDER BY ProductPurchased DESC; 

-- What are the product recalled behavior? 

SELECT ProductRecalled, COUNT(*) AS PurchaseQuality, SUM(PurchaseAmount) AS Total_Sales 
FROM TransactionData 
Group BY ProductRecalled 
ORDER BY SUM(PurchaseAmount) DESC; 

-- Social Media Data 
-- what are the likes behavior per platform? 

SELECT platform, SUM(EngagementLikes) AS Total_Likes, ROUND (AVG(EngagementLiKes),0) AS Average_Likes 
FROM SocialMedia 
GROUP BY platform 
ORDER BY SUM(EngagementLikes) DESC;

-- what are the Shares behavior per platform? 

SELECT platform, SUM(EngagementShares) AS Total_Shares, ROUND (AVG(EngagementShares),0) AS Average_Shares 
FROM SocialMedia 
GROUP BY platform 
ORDER BY SUM(EngagementShares) DESC; 


-- what are the Comments behavior per platform? 

SELECT platform, SUM(EngagementComments) AS Total_Comments, ROUND (AVG(EngagementComments),0) AS Average_Comments 
FROM SocialMedia 
GROUP BY platform 
ORDER BY SUM(EngagementComments) DESC; 

-- what is the sentiment distribution? 

SELECT Sentiment, COUNT(*) AS Sentiment_count 
FROM SocialMedia 
GROUP BY Sentiment 
ORDER BY COUNT (*) DESC; 

-- Brand Mentions vs Competitor mentions 

SELECT SUM(CASE 
WHEN BrandMention = 'True' THEN 1 
ELSE 0 
END) AS BrandMention_Count, 
SUM(CASE 
WHEN CompetitorMention = 'True' THEN 1 
ELSE 0 
END) AS CompetitorMentionCount
FROM SocialMedia; 
 

-- Response Time 
SELECT AVG(DATE_PART('epoch', CAST(firstresponse AS timestamp) - 
CAST(crisiseventtime AS timestamp)) / 3600) AS averageresponsetimehours FROM SocialMedia 
WHERE crisiseventtime IS NOT NULL 
AND firstresponse IS NOT NULL 


-- Top Influencers 
SELECT CustomerID, AVG(InfluencerScore) AS Influencescore FROM SocialMedia 
GROUP BY CustomerID 
ORDER BY Influencescore DESC 
LIMIT 10; 


-- Total Revenue by platform 
SELECT s.platform, sum(t.purchaseamount) AS Total_Revenue FROM SocialMedia s 
LEFT JOIN Transactiondata t 
ON s.customerid = t.customerid 
WHERE t.purchaseamount IS NOT NULL 
GROUP BY s.platform 
ORDER BY SUM(t.purchaseamount) 
LIMIT 5; 

-- Create a view for average purchase amount by product 
CREATE VIEW avg_purchase_per_product AS 
SELECT productpurchased, AVG(purchaseamount) AS avg_purchase_amount FROM transactiondata 
GROUP BY productpurchased; 
SELECT * FROM avg_purchase_per_product;
 ```
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
