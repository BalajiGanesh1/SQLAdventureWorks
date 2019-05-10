*********************************************** SQL ADVENTURE WORKS *****************************************************
SOURCE: https://sqlzoo.net/wiki/AdventureWorks_easy_questions

Q1:Show the first name and the email address of customer with CompanyName 'Bike World'

QUERY:

SELECT FirstName,EmailAddress 
FROM Customer
WHERE Customer.CompanyName='Bike World'

Q2:Show the CompanyName for all customers with an address in City 'Dallas'.

QUERY:

SELECT CompanyName
FROM Customer JOIN CustomerAddress
ON Customer.CustomerID= CustomerAddress.CustomerID
JOIN Address 
ON CustomerAddress.AddressID=Address.AddressID
WHERE City='Dallas'

Q3:How many items with ListPrice more than $1000 have been sold?

QUERY:

SELECT COUNT(SalesOrderDetailID)
FROM  SalesOrderDetail JOIN Product
ON Product.ProductID=SalesOrderDetail.ProductID
WHERE ListPrice>1000

Q4:Give the CompanyName of those customers with orders over $100000. Include the subtotal plus tax plus freight.

QUERY:

SELECT CompanyName 
FROM Customer JOIN SalesOrderHeader
ON Customer.CustomerID= SalesOrderHeader.CustomerID
WHERE (SubTotal+ TaxAmt+Freight) >100000

Q5:Find the number of left racing socks ('Racing Socks, L') ordered by CompanyName 'Riding Cycles'

QUERY:

SELECT COUNT(Name)
FROM Customer JOIN SalesOrderHeader
ON Customer.CustomerID= SalesOrderHeader.CustomerID
JOIN SalesOrderDetail
ON SalesOrderHeader.SalesOrderID= SalesOrderDetail.SalesOrderID
JOIN Product
ON SalesOrderDetail.ProductID=Product.ProductID
WHERE (Name='Racing Socks, L')AND (CompanyName='Riding Cycles');

Q6:A "Single Item Order" is a customer order where only one item is ordered. Show the SalesOrderID and the UnitPrice for every Single Item Order.

QUERY:

SELECT SalesOrderID,UnitPrice FROM SalesOrderDetail
WHERE SalesOrderID IN (SELECT s.salesOrderID as salesOrderId 
FROM SalesOrderDetail s
GROUP BY SalesOrderID
HAVING COUNT(*) = 1
)




Q7:Where did the racing socks go? List the product name and the CompanyName for all Customers who ordered ProductModel 'Racing Socks'.

QUERY:

SELECT
  Product.name, Customer.CompanyName
FROM
  ProductModel
JOIN
  Product
  ON ProductModel.ProductModelID = Product.ProductModelID
JOIN
  SalesOrderDetail
  ON SalesOrderDetail.ProductID = Product.ProductID
JOIN
 SalesOrderHeader
 ON SalesOrderDetail.SalesOrderID = SalesOrderHeader.SalesOrderID
JOIN
 Customer
 ON SalesOrderHeader.CustomerID = Customer.CustomerID
WHERE
  ProductModel.Name = 'Racing Socks';

Q8:Show the product description for culture 'fr' for product with ProductID 736.

QUERY:
 SELECT ProductDescription.Description
 FROM ProductDescription
   JOIN
     ProductModelProductDescription
   ON
 ProductDescription.ProductDescriptionID=ProductModelProductDescription.ProductDescriptionID
 JOIN
    ProductModel
    ON ProductModelProductDescription.ProductModelID = ProductModel.ProductModelID
  JOIN
    Product
    ON ProductModel.ProductModelID = Product.ProductModelID
WHERE ProductID=736 
AND
   Culture='fr'



Q9:Use the SubTotal value in SaleOrderHeader to list orders from the largest to the smallest. For each order show the CompanyName and the SubTotal and the total weight of the order.

QUERY:

SELECT CompanyName, SubTotal ,(OrderQty*Weight) AS TotalWeight
FROM Product
  JOIN
    SalesOrderDetail
    ON Product.ProductID = SalesOrderDetail.ProductID
  JOIN
    SalesOrderHeader
    ON SalesOrderDetail.SalesOrderID = SalesOrderHeader.SalesorderID
  JOIN
    Customer
    ON SalesOrderHeader.CustomerID = Customer.CustomerID
ORDER BY
  SalesOrderHeader.SubTotal DESC;
 


Q10:How many products in ProductCategory 'Cranksets' have been sold to an address in 'London'?

QUERY:

SELECT
  SUM(OrderQty)
FROM
  ProductCategory
  JOIN
    Product
    ON ProductCategory.ProductCategoryID = Product.ProductCategoryID
JOIN
    SalesOrderDetail
    ON Product.ProductID = SalesOrderDetail.ProductID
 JOIN
    SalesOrderHeader
    ON SalesOrderDetail.SalesOrderID = SalesOrderHeader.SalesorderID
 JOIN
    Address
    ON SalesOrderHeader.ShipToAddressID = Address.AddressID
WHERE
  Address.City='London'
  AND ProductCategory.Name='Cranksets'



Q11:

QUERY:
select C.FirstName,MO.AddressLine1,S.AddressLine1 from Customer C
inner join
(select A.AddressLine1,A.AddressID,B.CustomerID from Address A
inner join CustomerAddress B on A.AddressID = B.AddressID
where B.AddressType = 'Main Office' and A.City='Dallas') MO
on C.CustomerID = MO.CustomerID
inner join
(select A.AddressLine1,A.AddressID,B.CustomerID from Address A
inner join CustomerAddress B on A.AddressID = B.AddressID
where B.AddressType = 'Shipping') S on
C.CustomerID = S.CustomerID
 

Q12:For each order show the SalesOrderID and SubTotal calculated three ways: 
A) From the SalesOrderHeader 
B) Sum of OrderQty*UnitPrice 
C) Sum of OrderQty*ListPrice

QUERY:

SELECT
  SalesOrderHeader.SalesOrderID,
  SalesOrderHeader.SubTotal AS SubTotalA,
  SUM(SalesOrderDetail.OrderQty * SalesOrderDetail.UnitPrice) AS SubtotalB,
  SUM(SalesOrderDetail.OrderQty * Product.ListPrice) AS SubTotalC
FROM
  SalesOrderHeader
  JOIN
    SalesOrderDetail
    ON SalesOrderHeader.SalesOrderID = SalesOrderDetail.SalesOrderID
  JOIN
    Product
    ON SalesOrderDetail.ProductID = Product.ProductID
GROUP BY
  SalesOrderHeader.SalesOrderID,
  SalesOrderHeader.SubTotal;

Q13:Show the best selling item by value.

QUERY:

SELECT
  Product.Name,
  SUM(SalesOrderDetail.OrderQty * SalesOrderDetail.UnitPrice) AS TotalValue
FROM
  Product
  JOIN
    SalesOrderDetail
    ON Product.ProductID = SalesOrderDetail.ProductID
GROUP BY
  Product.Name
ORDER BY
  TotalValue DESC
LIMIT 1;

Q14:Show how many orders are in the following ranges (in $):

    RANGE      Num Orders      Total Value
    0-  99
  100- 999
 1000-9999
10000-

Query:

SELECT
  t.range AS 'RANGE',
  COUNT(Value) AS 'Number of Orders',
  SUM(Value) AS 'Total Value'
FROM
  (
    SELECT
    CASE
      WHEN
        SalesOrderDetail.UnitPrice * SalesOrderDetail.OrderQty BETWEEN 0 AND 99
      THEN
        '0-99'
      WHEN
        SalesOrderDetail.UnitPrice * SalesOrderDetail.OrderQty BETWEEN 100 AND 999
      THEN
        '100-999'
      WHEN
        SalesOrderDetail.UnitPrice * SalesOrderDetail.OrderQty BETWEEN 1000 AND 9999
      THEN
        '1000-9999'
      WHEN
        SalesOrderDetail.UnitPrice * SalesOrderDetail.OrderQty > 10000
      THEN
        '10000-'
      ELSE
        'Error'
    END AS 'Range',
    SalesOrderDetail.UnitPrice * SalesOrderDetail.OrderQty AS Value
  FROM
    SalesOrderDetail
  ) t
GROUP BY
  t.range;



