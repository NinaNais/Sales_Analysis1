# Sales Analysis E-commerce Bike Store
#### 

## Table of Contents

- [Project Overview](#projectoverview)
- [Business Request/User Stories](#business-requestuser-stories)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleansing & Transformation (SQL & Power Query)](#data-cleansing&transformation)
- [Results](#results)

### Project Overview

This data analysis project aims to provide insights into the sales performance of e-commerce company over the 2021-2023 period. By analysing various aspects of sales data I seek to identify trends that will aid in making data-driven business decisions and gaining a deeper understanding of the company's performance.

### Business Request / User Stories

Business Demand Overview:

- What are the company's essential financial performance indicators?
- What is the overall sales trend? 
- What are peak sales periods?
- Which products are top sellers?
- What is the Customer profile?

User Stories:

|No #|As a (role)|I want (request/demand)|So that I (user value)|Acceptance Criteria|
|----|-----------|----------------------|----------------------|-------------------|
|1| Sales Manager| To get a dashboard overview of Internet sales|Can follow better which customers and products sell the best| A Power BI dashboard with graphs comparing against the budget|
|2| Marketing Manager| A detailed overview of Internet sales per Customers|Can follow up with the group of customers who buy the most and who we can sell more to|A Power BI dashboard which gives me insight into who are our key clients and what they are buying|
|3| Sales Representative| A detailed overview of Internet sales per Products|Can follow up my Products that sell the most| A Power BI dashboard which allows me to filter data for each Product|

### Data Sources

The primary dataset for this project is the "Database_Bike.zip" file, which contains a detailed database in csv files. One data source (sales budgets) were provided in Excel format.

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

After loading tables into Power BI, I made several data transformations using the Power Query editor. For instance, I fixed the data in the "Product Subcategory" column. Initially, some cells had NULL values, while in others, the Product Subcategory did not match the Category and Product Name. I added a conditional column to fix those mismatches. Then I have created basic measures such as Revenue, Profit, Number of Customers, Margin. Additionally, I created a DAX column to prepare Age and Income Buckets required for my visualizations.

### Results

"Sales_Analysis_Bike.pbix" contains full report.
The sales management dashboard consists of one page functioning as a dashboard and overview, with two additional pages dedicated to combining tables for necessary details and visualizations illustrating sales per customers and per products.


![Zrzut ekranu 2024-09-16 100737 — kopia](https://github.com/user-attachments/assets/d6893e97-2f2b-4378-b7bf-aad489cd0eeb)

![Zrzut ekranu 2024-09-16 175523](https://github.com/user-attachments/assets/1c80a919-ac6a-4571-8cee-c082e0f065b8)

![Zrzut ekranu 2024-09-16 175546](https://github.com/user-attachments/assets/ebde13bb-e8cf-4386-8a3d-787df14b43c0)
