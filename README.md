# Sales Analysis E-commerce Bike Store
#### 

## Table of Contents

- [Project Overview](#overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Exploratory Data Analysis](exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recomendations](#recomendations)
- [Limitations](#limitations)

### Project Overview

IThis data analysis project aims to provide insights into the sales performance of e-commerce company over the 2021-2023 period. By analysing various aspects of sales data I seek to identify trends that will aid in making data-driven business decisions and gaining a deeper understanding of the company's performance.

### Business Request / User Stories

Business Demand Overview:

- What are the company's essential financial performance indicators?
- What is the overall sales trend? 
- What are peak sales periods?
- Which products are top sellers?
- What is the Customer profile?

User Stories:
No #As a (role)I want (request/demand) So that I (user value) Acceptance Criteria
1 Sales Manager To get a dashboard overview of Internet salesCan follow better which customers and products sell the best A Power BI dashboard with graphs comparing against the budget.
2 Marketing Manager A detailed overview of Internet sales per CustomersCan follow up with the group of customers who buy the most and who we can sell more to A Power BI dashboard which gives me insight into who are our key clients and what they are buying
3 Sales Representative A detailed overview of Internet sales per ProductsCan follow up my Products that sell the most A Power BI dashboard which allows me to filter data for each Product

### Data Sources

The primary dataset for this example is the" file, which contains a detailed travel agency database.

### Tools

- SQL Server - Data gaining, cleaning and transforming
- Power Query - Data transforming
- PowerBI - Creating Reports


### Data Cleansing & Transformation (SQL & Power Query)

To create the necessary data model for doing analysis and fulfilling the business needs defined in the user stories the following tables were extracted using SQL.

One data source (sales budgets) were provided in Excel format and were connected in the data model in a later step of the process.

Below are the SQL statements for cleansing and transforming necessary data:

FactSales:

```sql
SELECT[ProductKey]
      --,[OrderDateKey]--
      --,[DueDateKey]--
      --,[ShipDateKey]--
      ,[CustomerKey]
      ,[PromotionKey]
      --,[CurrencyKey]--
      --,[SalesTerritoryKey]--
	  ,g.City as [Sales City]
	  ,st.SalesTerritoryCountry as [Sales Country]
      ,[SalesOrderNumber]
      --,[SalesOrderLineNumber]--
      --,[RevisionNumber]--
      ,[OrderQuantity]
      ,[UnitPrice]
      --,[ExtendedAmount]--
      ,[UnitPriceDiscountPct]
      ,[DiscountAmount]
      --,[ProductStandardCost]--
      ,[TotalProductCost]
      ,[SalesAmount]
      --,[TaxAmt]--
      --,[Freight]--
      --,[CarrierTrackingNumber]--
      --,[CustomerPONumber]--
      ,[OrderDate]
      --,[DueDate]--
     -- ,[ShipDate]---
  FROM FactInternetSales fis
  left join DimGeography g on fis.SalesTerritoryKey=g.SalesTerritoryKey
  left join DimSalesTerritory st on fis.SalesTerritoryKey=st.SalesTerritoryKey
  where LEFT(OrderDateKey,4) >=2021
```

DimProduct:

```sql
SELECT [ProductKey]
      ,[ProductAlternateKey] as ProductItemCode
      --,[ProductSubcategoryKey]--
      --,[WeightUnitMeasureCode]--
      --,[SizeUnitMeasureCode]--
      ,[EnglishProductName] as [Product Name]
	  ,pc.EnglishProductCategoryName as [Product Category Name]
	  ,ps.EnglishProductSubcategoryName as [Product Subcategory Name]
      --,[SpanishProductName]--
      --,[FrenchProductName]--
      --,[StandardCost]--
      --,[FinishedGoodsFlag]--
      ,[Color] as [Product Color]
      --,[SafetyStockLevel]--
      --,[ReorderPoint]--
      --,[ListPrice]--
      ,[Size] as [Product Size]
      --,[SizeRange]--
      --,[Weight]--
      --'[DaysToManufacture]--
      ,[ProductLine] as [Product Line]
      --,[DealerPrice]--
      --,[Class]--
      --,[Style]--
      ,[ModelName] as [ Product Model Name]
      --,[LargePhoto]--
      ,[EnglishDescription] as [Product Description]
      --,[FrenchDescription]--
      --,[ChineseDescription]--
      --,[ArabicDescription]--
      --,[HebrewDescription]--
      --,[ThaiDescription]--
      --,[GermanDescription]--
      --,[JapaneseDescription]--
      --,[TurkishDescription]--
      --,[StartDate]--
      --,[EndDate]--
      ,isnull(status, 'Outdated') as [Product Status]
  FROM [dbo].[DimProduct] p
  left join DimProductCategory pc on p.ProductSubcategoryKey=pc.ProductCategoryKey
  left join DimProductSubcategory ps on p.ProductSubcategoryKey=ps.ProductSubcategoryKey
  order by p.ProductKey
```

DimCustomer:

```sql
SELECT
       [CustomerKey]
      --,[GeographyKey]--
      --,[CustomerAlternateKey]--
      --,[Title]--
      ,[FirstName] as [First Name]
      --,[MiddleName]--
      ,[LastName] as [Last Name]
	  ,FirstName +' '+LastName as [Full Name]
      --,[NameStyle]--
      ,[BirthDate]
	  ,case [MaritalStatus] when 'M' then 'Partner' else 'Single' end as [Relationship Status]
      --,[Suffix]--
      ,case [Gender] when 'M' then 'Male' else 'Female' end as Gender
      --,[EmailAddress]--
      ,[YearlyIncome] as[Yearly Income]
      --,[TotalChildren]--
      ,[NumberChildrenAtHome] as [Number Children At Home]
      --,[EnglishEducation]--
      --,[SpanishEducation]--
      --,[FrenchEducation]--
      ,[EnglishOccupation] as Occupation
      --,[SpanishOccupation]--
     -- ,[FrenchOccupation]--
      --,[HouseOwnerFlag]--
     -- ,[NumberCarsOwned]--
      --,[AddressLine1]--
      --,[AddressLine2]--
      --,[Phone]--
      ,[DateFirstPurchase] as [Date First Purchase]
      --,[CommuteDistance]--
	  ,g.City
	  ,g.EnglishCountryRegionName as Country
	  ,YEAR(getdate())-1-year(BirthDate) as [Customer Age]
  FROM DimCustomer c
  left join DimGeography g 
  on c.GeographyKey=g.GeographyKey
  order by CustomerKey
```

Dim Date:

```sql
SELECT [DateKey]
      ,[FullDateAlternateKey] as Date
      ,[DayNumberOfWeek] as DayOfWeekNo
      ,[EnglishDayNameOfWeek] as Day
      --,[SpanishDayNameOfWeek]--
      --,[FrenchDayNameOfWeek]--
      --,[DayNumberOfMonth]
      --,[DayNumberOfYear]--
      ---,[WeekNumberOfYear]---
      ,[EnglishMonthName] as Month
	  ,left(EnglishMonthName,3) as MonthShort
      --,[SpanishMonthName]--
      --,[FrenchMonthName]--
      ,[MonthNumberOfYear] as MonthNo
      ,[CalendarQuarter] as Quarter
      ,[CalendarYear] as Year
      --,[CalendarSemester]--
      --,[FiscalQuarter]--
      --,[FiscalYear]--
      --,[FiscalSemester]--
  FROM [AdventureWorksDW2019].[dbo].[DimDate]
  where CalendarYear >=2021
```



### Data Analysis

1. What is the current stock status?
```sql
select Sold.Name, Ordered.Ordered_Number-Sold.Sold_Number as Stock_status
from
(select p.Name, sum(s.Amount) as Sold_Number
from Sales s
inner join Products p on s.ProductID=p.ProductID
group by p.Name) Sold
inner join
(select p.Name, sum(obp.Amount) as Ordered_Number
from OrdersByProduct obp
inner join Products p on obp.ProductID=p.ProductID
group by p.Name) Ordered
on Sold.Name=Ordered.Name
order by Stock_status 
```


### Results/Findings

The analysis results are summarized as follows:
1. The company's sales have been steadily increasing over the past year, experiencing a noticeable peak during the holiday season.
2. Product Category A is the best-performing category in terms of sales and revenue.
3. Customer segments with high lifetime value(LIV) should be targeted for marketing efforts. 


### Recomendations

Based on the analysis, I recommend the following actions:
-Invest in marketing and promotion during peak sales seasons to maximize revenue.
-Focus on expanding and promoting products in Category A
- Implement a customer segmentation strategy to target high-LTV customers effectively.


### Limitations

I had to remove all zero values from the budget and revenue columns because would have affected the accuracy of my conclusion from the analysis. There are still a few outliers even after the omissions but even then we can still see that there is a positive correlation between both budget and number of votes with revenue.
