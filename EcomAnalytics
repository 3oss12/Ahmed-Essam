#Era Soft Graduation Project


use ecommercedb2;

create view Top_Products As
select ProductName,Price from products
where Price between 50 and 200
order by Price DESC;


select * from customers;

select * from calendar;

create view Customers_Registration As

select concat(c.FirstName," ",C.LastName) As Full_Name,cal.YEAR,a.Country  from customers c
inner join calendar cal on c.RegistrationDateID = cal.DateID
inner join address a on c.AddressID = a.AddressID
where cal.YEAR = 2024 and a.Country = "USA"
group by concat(c.FirstName," ",C.LastName),cal.YEAR,a.Country
order by Full_Name ASC;

Select p.ProductName, cat.CategoryName,p.Price from products p
inner join categories cat on p.CategoryID = cat.CategoryID
where CategoryName = "Electronics"
order by Price DESC;

SET SQL_SAFE_UPDATES = 0;


select * from orders
where TotalAmount = 0;

DELETE FROM orders
WHERE TotalAmount = 0;


UPDATE products p
INNER JOIN categories cat ON p.CategoryID = cat.CategoryID
SET p.Price = p.Price * 0.01
WHERE cat.CategoryName = 'Electronics';

create view Top_Customers_2024 As

select concat(c.FirstName," ",c.LastName) As Full_Name,Cal.YEAR, count(o.OrderID) As Total_Orders,sum(o.TotalAmount) As Total_Amount from customers c
inner join calendar cal on c.RegistrationDateID = cal.DateID
inner join orders o on c.CustomerID = o.CustomerID
where cal.YEAR = 2024
group by c.CustomerID
Having sum(o.TotalAmount) > 5000
order by Total_Amount DESC;

select p.ProductName, Avg(p.Price) from products p
group by p.ProductID;

select p.ProductName, sum(p.price), count(ProductID) from products p
group by p.ProductID;

SELECT ProductName,Price,(SELECT AVG(Price) FROM products) AS AveragePrice from products
WHERE Price > (SELECT AVG(Price) FROM products)
order by Price Desc;

SELECT concat(c.FirstName," ",c.LastName) As FullName, p.ProductName, p.Price from products p
inner join orderdetails od on p.ProductID = od.ProductID
inner join orders o on od.OrderID = o.OrderID
inner join Customers c on o.CustomerID = c.CustomerID
WHERE Price = (SELECT MAX(Price) FROM products)
order by FullName ASC;

DELIMITER $$

CREATE PROCEDURE GetOrdersByCustomer(IN p_CustomerID INT)
BEGIN
    SELECT 
        CONCAT(c.FirstName, ' ', c.LastName) AS FullName,
        o.OrderID,
        o.TotalAmount
    FROM orders o
    INNER JOIN customers c ON o.CustomerID = c.CustomerID
    WHERE c.CustomerID = p_CustomerID;
END$$

DELIMITER ;

call GetOrdersByCustomer(10);

WITH Product_Sales AS (
    SELECT 
        p.ProductID,
        p.ProductName,
        p.CategoryID,
        SUM(od.Quantity * od.UnitPrice) AS TotalSales
    FROM products p
    INNER JOIN orderdetails od ON p.ProductID = od.ProductID
    GROUP BY p.ProductID, p.ProductName, p.CategoryID
),
RankedProducts AS (
    SELECT 
        ps.ProductID,
        ps.ProductName,
        ps.CategoryID,
        ps.TotalSales,
        DENSE_RANK() OVER (PARTITION BY ps.CategoryID ORDER BY ps.TotalSales DESC) AS rnk
    FROM Product_Sales ps
)
SELECT 
    c.CategoryName,
    rp.ProductName,
    rp.TotalSales,
    rp.rnk AS RankInCategory
FROM RankedProducts rp
INNER JOIN categories c ON rp.CategoryID = c.CategoryID
WHERE rp.rnk <= 3
ORDER BY c.CategoryName, rp.rnk;

SELECT 
    cal.Month,
    CASE cal.Month
        WHEN 1 THEN 'January'
        WHEN 2 THEN 'February'
        WHEN 3 THEN 'March'
        WHEN 4 THEN 'April'
        WHEN 5 THEN 'May'
        WHEN 6 THEN 'June'
        WHEN 7 THEN 'July'
        WHEN 8 THEN 'August'
        WHEN 9 THEN 'September'
        WHEN 10 THEN 'October'
        WHEN 11 THEN 'November'
        WHEN 12 THEN 'December'
    END AS MonthName, cal.YEAR,
    SUM(od.Quantity * od.UnitPrice) AS TotalSales
FROM orderdetails od
INNER JOIN orders o ON od.OrderID = o.OrderID
INNER JOIN calendar cal ON o.OrderDateID = cal.DateID
WHERE cal.YEAR = 2024
GROUP BY cal.Month, cal.YEAR
ORDER BY cal.Month ASC;

select o.OrderID,o.TotalAmount,cal.DATE,cal.DAYOFWEEK from orders o
inner join calendar cal on o.OrderDateID = cal.DateID
where cal.DAYOFWEEK IN ("Saturday","Sunday")
group by o.OrderID,o.TotalAmount,cal.DATE,cal.DAY
order by TotalAmount DESC;

select AVG(DATEDIFF(ca.Date, cal.Date)) AS AvgDeliveryDays from orders o
inner join calendar cal on o.OrderDateID = cal.DateID
inner join calendar ca on o.DeliveryDateID = ca.DateID;


select cat.CategoryName,sum(od.Quantity)As TotalQuantity from orderdetails od
inner join products p on od.ProductID = p.ProductID
inner join categories cat on p.CategoryID = cat.CategoryID
group by cat.CategoryName
order by TotalQuantity DESC
limit 1;

select concat(c.FirstName," ",c.LastName) As FullName,count(o.OrderID) As TotalOrders,DATEDIFF(
        (SELECT MAX(cal2.Date) FROM orders o2 INNER JOIN Calendar cal2 ON o2.OrderDateID = cal2.DateID),MAX(cal.Date)) AS DaysSinceLastOrder from orders o
inner join Customers c on o.CustomerID = c.CustomerID
inner join Calendar cal on o.OrderDateID = cal.DateID
WHERE cal.Date >= DATE_SUB(
    (SELECT MAX(cal2.Date) 
     FROM orders o2
     INNER JOIN Calendar cal2 ON o2.OrderDateID = cal2.DateID), 
    INTERVAL 90 DAY)
    group by  concat(c.FirstName," ",c.LastName)
order by TotalOrders DESC
limit 5;

select concat(c.FirstName," ",c.LastName) As FullName,sum(o.TotalAmount) As TotalAmount,cal.YEAR, Rank() over (Order by sum(o.TotalAmount) Desc) As CustomerRank 
from orders o
inner join customers c on o.CustomerID = c.CustomerID
inner join calendar cal on o.OrderDateID = cal.DateID
where cal.YEAR = 2024
group by concat(c.FirstName," ",c.LastName),cal.YEAR
order by TotalAmount DESC;


