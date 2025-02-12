# Incubyte_assessment

1) Analyzing all the Transactions
   
Total Num of Transaction- SELECT COUNT(TransactionID) AS Total_Num_Transactions FROM assessment_dataset

Total Amount- SELECT SUM(TransactionAmount) AS TotalTransactionAmount FROM assessment_dataset

Average Amount- SELECT AVG(TransactionAmount) AS AverageTransactionAmount FROM assessment_dataset

Min & Max- SELECT MAX(TransactionAmount) AS MaxTransactionAmount, MIN(TransactionAmount) AS MinTransactionAmount FROM assessment_dataset

3) Analyzing Customers
   
Unique Customers- SELECT COUNT(DISTINCT CustomerID) AS UniqueCustomers FROM assessment_dataset WHERE CustomerID IS NOT NULL

Average Age- SELECT AVG(CustomerAge) AS AverageCustomerAge FROM assessment_dataset WHERE CustomerAge IS NOT NULL

Gender Distribution- SELECT CustomerGender, COUNT(*) AS Count FROM assessment_dataset GROUP BY CustomerGender

Top 5 city with highest no. of Transcations- SELECT City, COUNT(*) AS assessment_dataset FROM transactions GROUP BY City ORDER BY TransactionCount DESC LIMIT 5

5) Product and Store Analysis
   
Top 10 Products- SELECT ProductName, COUNT(*) AS Count FROM assessment_dataset GROUP BY ProductName ORDER BY COunt DESC LIMIT 10

Total QUantity by product- SELECT ProductName, SUM(Quantity) AS TotalQuantity FROM assessment_dataset GROUP BY ProductName ORDER BY TotalQuantity DESC

Online VS InStore- SELECT StoreType, COUNT(*) AS Count FROM assessment_dataset GROUP BY StoreType
Revenue by store type- SELECT StoreType, SUM(TransactionAmount) AS TotalRevenue FROM assessment_dataset GROUP BY StoreType

7) Discounts/ Loyalty
   
Average Discount- SELECT AVG(DiscountPercent) AS AverageDiscountPercent FROM assessment_dataset

Total Loyalty Points- SELECT SUM(LoyaltyPoints) AS TotalLoyaltyPoints FROM assessment_dataset

9) Delivery & Returns
    
Average Delivery- SELECT AVG(DeliveryTimeDays) AS AverageDeliveryTime FROM assessment_dataset

Average Shipping Cost- SELECT AVG(ShippingCost) AS AverageShippingCost FROM assessment_dataset

Count of Returns(Yes/No)- SELECY COUNT(*) as COUNT from assessment_dataset group by RETURNED

Average Feedback Score- SELECT AVG(FeedBackStore) from assessment_dataset


-- Some More Analysis Using Window Functions

1) Cumulative Transaction Amount per Customer - 
select 
    CustomerID, 
    TransactionDate, 
    TransactionAmount, 
    SUM(TransactionAmount) OVER (PARTITION BY CustomerID ORDER BY TransactionDate ASC) AS CumulativeTransactionAmount
FROM assessment_dataset
WHERE CustomerID IS NOT NULL
ORDER BY CustomerID, TransactionDate

2) Ranking Customers by Total Spend
select 
    CustomerID, 
    SUM(TransactionAmount) AS TotalSpend,
    DENSE_RANK() OVER (ORDER BY SUM(TransactionAmount) DESC) AS SpendRank
FROM assessment_dataset
GROUP BY CustomerID
ORDER BY SpendRank

3) Percentile Rank of Customers by Transaction Amount
select 
    CustomerID, 
    TransactionAmount, 
    PERCENT_RANK() OVER (ORDER BY TransactionAmount DESC) AS P_Rank
FROM assessment_dataset
WHERE CustomerID IS NOT NULL


4) Rolling Average of Transaction Amount (one week window)
select 
    TransactionDate, 
    AVG(TransactionAmount) OVER (ORDER BY TransactionDate ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS RollingAverage
FROM assessment_dataset

5) First and Last Transaction Date per Customer
select 
    CustomerID, 
    MIN(TransactionDate) OVER (PARTITION BY CustomerID) AS FirstTransactionDate,
    MAX(TransactionDate) OVER (PARTITION BY CustomerID) AS LastTransactionDate
FROM assessment_dataset

6) Difference in Transaction Amounts (using the LAG function)
SELECT 
    TransactionID, 
    CustomerID, 
    TransactionDate, 
    TransactionAmount, 
    LAG(TransactionAmount) OVER (PARTITION BY CustomerID ORDER BY TransactionDate) AS PreviousTransactionAmount,
    TransactionAmount - LAG(TransactionAmount) OVER (PARTITION BY CustomerID ORDER BY TransactionDate) AS AmountDifference
FROM assessment_dataset

7) YOY Growth in Transaction Amount
SELECT 
    EXTRACT(YEAR FROM TO_DATE(TransactionDate, 'MM/DD/YYYY')) AS Year, 
    SUM(TransactionAmount) AS TotalAmount,
    SUM(TransactionAmount) - LAG(SUM(TransactionAmount)) OVER (ORDER BY EXTRACT(YEAR FROM TO_DATE(TransactionDate, 'MM/DD/YYYY'))) AS YoYGrowth
FROM assessment_dataset
GROUP BY EXTRACT(YEAR FROM TO_DATE(TransactionDate, 'MM/DD/YYYY'))
ORDER BY Year

8) Most Recent Feedback Score per Customer
SELECT 
    CustomerID, 
    FIRST_VALUE(FeedbackScore) OVER (PARTITION BY CustomerID ORDER BY TransactionDate DESC) AS MostRecentFeedbackScore
FROM assessment_dataset

9) Running Total SUM(Prefix Sum) of Loyalty Points Awarded
SELECT 
    TransactionDate, 
    LoyaltyPoints, 
    SUM(LoyaltyPoints) OVER (ORDER BY TransactionDate) AS RunningTotalLoyaltyPoints
FROM transactions

10) Average Delivery Time per Region 
SELECT 
    Region, 
    AVG(DeliveryTimeDays) OVER (PARTITION BY Region) AS AverageDeliveryTime
FROM assessment_datasetx







