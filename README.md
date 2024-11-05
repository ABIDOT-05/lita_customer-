# lita_customer
## customer data

[project overview](project-overview)

[Data Sources](Data-Sources)

[Tools Used](Tools-Used)

[Data Cleaning and Preparation](Data-Cleaning-and-Preparation)

[Exploratory Data Analysis](Exploratory-Data-Analysis)

[Data Analysis](Data-Analysis)

[Data Summary](Data-Summary)

### project overview
This project aims to analyze customer subscription data to gain insights into subscription trends , and customer behavior. The results will guide decision-making in areas like customer retention, marketing strategies, and subscription plan optimization.

### Data Sources
The primary source of this data is Customerdata.xslx, from lita capstone file.

### Tools Used 
1. Microsoft Excel for :
- data cleaning
- data analysis
- data summary with pivot tables 
2. SQL - Structured Query Language for data query

3. PowerBi for data visualization

4. Github for Portfolio

### Data Cleaning and Preparation

- data handling and inspection
- addressing any missing, inconsistent, or erroneous values.
- dealing with duplicate values
- deleting empty rows and mising values
- Data cleaning and formatting

  ### Exploratory Data Analysis
This involves exploring the Data to answer some questions about it such as
- What are the counts and percentages of each SubscriptionType?
- Find the total number of active and canceled subscriptions
- What is the total Revenue generated across all customers?
- What is the total  Revenue by Region?
- What's the distribution of active subscription per region
- calculate total revenue by subscription type.
- find the distribution of subscribtion type by region

  ### Data Analysis
  In Excel for initial data cleaning and pivot tables

![image](https://github.com/user-attachments/assets/4bba075a-204f-4d65-b5ab-8a48f775f162)


![image](https://github.com/user-attachments/assets/5e421059-7e5a-4252-ae55-f40db77a45f1)

  SQL Server: To store, manage, and query large volumes of the Customer data

  ``` SQL
  ALTER TABLE [dbo].[CustomerData]
  SELECT DISTINCT *                                            -
  INTO CUSTOMER                                                -                                                         
   FROM [dbo].[CustomerData]
  WHERE CustomerName IS NOT NULL 
  AND customerid IS NOT NULL 
  AND SubscriptionType IS NOT NULL
  and region is not null
  and subscriptionstart is not null
  and subscriptionend is not null
  and Canceled is not null
  and Revenue is not null;

  SELECT   COUNT(*)   FROM [dbo].[CUSTOMER]
  create view vw_dddddd as 
  SELECT DISTINCT 
  CustomerID,CustomerName,avg(Revenue) as Average_revenue from [dbo].[CUSTOMER]
  group by CustomerID, CustomerName;

  CREATE VIEW REAL_DATA AS 
  SELECT DISTINCT
    T.CustomerID,
	T.CustomerName,
    T.Region, 
    T.subscriptionType,
    T.subscriptionStart,
    T.subscriptionEnd ,
    T.Canceled,
	V.Average_revenue
  FROM 
     [dbo].[CUSTOMER] T
  JOIN 
    vw_dddddd V ON T.CustomerID = V.CustomerID;

  --- retrieve the total number of customers from each region.
  create view customer_by_region as
  select Region, COUNT(customerid)  AS COUNT_OFCUSTOMER from REAL_DATA
  group by Region
  SELECT* FROM [dbo].[customer_by_region]

  ---Find the most popular subscription type by the number of customers.
  CREATE VIEW PopularSubscriptionType as
  select subscriptiontype, COUNT(customerID) as count from [dbo].[REAL_DATA]
  group by subscriptiontype
  select * from PopularSubscriptionType

  --- find customers who canceled their subscription within 6 months.
  create view less_than6mnths as 
  SELECT 
    CustomerID, 
    CustomerName, 
    SubscriptionStart, 
    SubscriptionEnd
  FROM 
    [dbo].[REAL_DATA]
  WHERE 
    SubscriptionEnd >= DATEADD(MONTH, -6, GETDATE()) 
    AND Canceled = '1';  


   ----calculate the average subscription duration for all customers.
  create view AverageDuration as
  select CUSTOMERID, AVG(datediff(day,subscriptionstart,subscriptionend)) as subscription_duration
  from [dbo].[REAL_DATA]
  group by CUSTOMERid

  ---find customers with subscriptions longer than 12 months.
  alter view longer_than12mnths as 
  SELECT 
    CustomerID, 
     DATEDIFF(MONTH, SubscriptionStart, SubscriptionEnd) AS SubscriptionDuration
  FROM 
    [dbo].[REAL_DATA]
  WHERE 
   DATEDIFF(MONTH,SubscriptionStart, SubscriptionEnd) > 12;

   select* from longer_than12mnths


  --- calculate total revenue by subscription type.
  create view revenue_by_subsType as
  select subscriptiontype ,SUM(Average_revenue) as total_revenue
  from [dbo].[REAL_DATA]
  group by SubscriptionType

  select* from revenue_by_subsType


  ---find the top 3 regions by subscription cancellations
  create view Top3Regions as 
  select  top 3 region, COUNT(canceled) as cancellations
  from [dbo].[REAL_DATA]
  where Canceled = '1'
  group by Region
```

Create Power BI dashboards with key metrics, trends, and customer insights.
![customer Dashboard](https://github.com/user-attachments/assets/8734f8f0-8939-48c8-88ce-3823bd5d0a95)

### Data Summary

The dataset contains detailed information on customer subscriptions, with fields such as `CustomerID`, `CustomerName`, `Region`, `SubscriptionType`, `SubscriptionStart`, `SubscriptionEnd`, `Canceled`, and `Revenue`. This well-structured dataset provides valuable insights into customer subscription patterns across four regions: North, South, East, and West.

Upon initial analysis, the dataset was found to contain duplicate entries. After removing duplicates and calculating average values where applicable, the dataset was refined to 20 unique rows, each representing a distinct customer with a unique `CustomerID` and `CustomerName`. Each region (North, South, East, West) contains an equal distribution of customers (5 per region). Notably, the West region generates the highest revenue.

The dataset includes three subscription types—Basic, Standard, and Premium—with Basic subscriptions yielding the highest revenue and being the most popular overall. The `Canceled` column indicates subscription status, showing that 55% of customers have active subscriptions, while 45% have canceled. Similarly, active subscribers account for 55% of total revenue, with canceled subscriptions contributing the remaining 45%.

On average, subscription duration is 365 days, indicating that most members continue for about a year before deciding to cancel or renew. Each region has a dominant subscription type: customers in the North and East prefer Basic plans, while the South primarily subscribes to Premium, and the West to Standard.

Among active subscriptions, Basic plans have the highest count with 7 active subscribers and 3 cancellations. Both Standard and Premium subscriptions have 2 active subscribers and 3 cancellations each. This breakdown highlights customer preferences and regional trends in subscription behavior, useful for targeted marketing and customer retention strategies.


  
