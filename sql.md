# Shopify Fall 2021 Data Science Intern Challenge 
## Written by: Dexter van Leeuwen
## Question 2

### a) How many orders were shipped by Speedy Express in total?
### answer: 54
Query:
```
SELECT s.ShipperName, COUNT(o.OrderID) AS num_SpExp_orders
FROM shippers AS s
JOIN orders AS o ON s.ShipperID = o.ShipperID
WHERE s.ShipperName = "Speedy Express"
GROUP BY s.ShipperName
```

### b) What is the last name of the employee with the most orders?
### answer: Peacock (40 orders)
Query:

Note: Join is an expensive operation, so selecting only the employee with the most orders prior to the join is best if querying on large tables.
```
SELECT e.LastName, num_orders
FROM 
(
	SELECT EmployeeID, COUNT(OrderID) AS num_orders
	FROM Orders
    GROUP BY EmployeeID
    ORDER BY num_orders DESC
    LIMIT 1
) 
AS o
JOIN Employees AS e ON e.EmployeeID = o.EmployeeID
```

Since our tables are small, this query might be more natural but isn't as efficient.
```
SELECT e.LastName, COUNT(o.OrderID) AS num_orders
FROM Orders AS o
JOIN Employees AS e ON e.EmployeeID = o.EmployeeID
GROUP BY e.EmployeeID
ORDER BY num_orders DESC
LIMIT 1
```

### c) What product was ordered the most by customers in Germany?
### answer: Boston Crab Meat (160 orders)
Query:

(Remove limit to see all `ProductName, product_quantity` pairs)
```
SELECT ProductName, SUM(Quantity) AS product_quantity
FROM (
	SELECT OrderID
    FROM Orders
	WHERE CustomerID IN (
      SELECT CustomerID 
      FROM Customers
      WHERE Country = "Germany"
	)
) AS german_oids
JOIN OrderDetails AS od ON german_oids.OrderID = od.OrderID
JOIN Products AS p ON p.ProductID = od.ProductID
GROUP BY p.ProductID
ORDER BY product_quantity DESC
LIMIT 1
```

