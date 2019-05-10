************************************************* SQL ADVENTURE WORKS ********************************************************************
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

Q12:

