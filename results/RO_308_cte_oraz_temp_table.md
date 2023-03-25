# 308. Common Table Expression oraz tabele tymczasowe - rozwiązania

1.  Znajdź klientów, którzy złożyli zamówienia w ciągu ostatnich 30 dni. Użyj CTE.

![Picture](images/images_results/30801.png)

```sql
WITH RecentOrders AS (
  SELECT DISTINCT customer_id
  FROM orders
  WHERE order_date >= DATEADD(day, -30, GETDATE())
)
SELECT *
FROM customers
WHERE customer_id IN (SELECT customer_id FROM RecentOrders);
```

---
2.  Znajdź zamówienia, które mają status 1 (`order_status` = 1). Użyj CTE.

![Picture](images/images_results/30802.png)

```sql
WITH InProgressOrders AS (
  SELECT *
  FROM orders
  WHERE order_status = 1
)
SELECT *
FROM InProgressOrders;
```

---
3.  Znajdź produkty, których cena jest mniejsza niż 50 zł. Użyj tabele tymczasowe.

![Picture](images/images_results/30803.png)

```sql
CREATE TABLE #CheapProducts (
  product_id INT,
  product_name VARCHAR(255),
  brand VARCHAR(155),
  price DECIMAL(10, 2)
)

INSERT INTO #CheapProducts
SELECT product_id, product_name, brand, price
FROM products
WHERE price < 50

SELECT *
FROM #CheapProducts;
```

---
4.  Znajdź zamówienia złożone przez klienta o identyfikatorze 10. Użyj tabele tymczasowe.

![Picture](images/images_results/30804.png)

```sql
CREATE TABLE #CustomerOrders (
  order_id INT,
  customer_id INT,
  order_status tinyint,
  order_date DATETIME,
  shipped_date DATETIME
)

INSERT INTO #CustomerOrders
SELECT order_id, customer_id, order_status, order_date, shipped_date
FROM orders
WHERE customer_id = 10

SELECT *
FROM #CustomerOrders;
```

---
5. Znajdź nazwy kategorii, które mają więcej niż 5 produktów. Zwróć również liczbę produktów dla każdej kategorii.

![Picture](images/images_results/30805.png)

```sql
WITH ProductCount AS (
  SELECT category_id, COUNT(*) AS ProductCount
  FROM products
  GROUP BY category_id
)
SELECT c.category_name, pc.ProductCount
FROM categories c
INNER JOIN ProductCount pc ON c.category_id = pc.category_id
WHERE pc.ProductCount > 5;
```

---
6. Znajdź klientów, którzy nie złożyli zamówienia w ciągu ostatnich 6 miesięcy, i przypisz im kategorię "Nieaktywni". Wynik zapisz do tabeli tymczasowej i wyświetl jej zawartość.

![Picture](images/images_results/30806.png)

```sql
CREATE TABLE #InactiveCustomers (
  customer_id INT,
  first_name VARCHAR(155),
  last_name VARCHAR(155),
  email VARCHAR(155),
  phone_number VARCHAR(50),
  inactive_category VARCHAR(50)
)

INSERT INTO #InactiveCustomers
SELECT c.customer_id, c.first_name, c.last_name, c.email, c.phone_number, 'Nieaktywni'
FROM customers c
WHERE NOT EXISTS (
  SELECT *
  FROM orders o
  WHERE o.customer_id = c.customer_id AND o.order_date >= DATEADD(month, -6, GETDATE())
)

SELECT * FROM #InactiveCustomers
```

---
7. Znajdź produkty, których ilość w magazynie jest mniejsza niż 10, i przypisz im kategorię "Niski stan magazynowy". Wynik zapisz do tabeli tymczasowej i wyświetl jej zawartość.

![Picture](images/images_results/30807.png)

```sql
CREATE TABLE #LowStockProducts (
  product_id INT,
  product_name VARCHAR(255),
  brand VARCHAR(155),
  price DECIMAL(10, 2),
  quantity INT,
  low_stock_category VARCHAR(50)
)

INSERT INTO #LowStockProducts
SELECT p.product_id, p.product_name, p.brand, p.price, pi.quantity, 'Niski stan magazynowy'
FROM products p
INNER JOIN product_inventories pi ON p.product_id = pi.product_id
WHERE pi.quantity < 10

SELECT * FROM #LowStockProducts
```

---
8. Znajdź adresy klientów, którzy zamówili produkty za więcej niż 1000 $ w ciągu ostatnich 3 miesięcy. Zwróć również liczbę zamówień i łączną wartość zamówień dla każdego adresu.

![Picture](images/images_results/30808.png)

```sql
WITH CustomerOrders AS (
  SELECT o.customer_id,
  SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) AS OrderValue,
  COUNT(*) AS OrderCount
  FROM orders o
  INNER JOIN order_items oi ON o.order_id = oi.order_id
  INNER JOIN products p ON oi.product_id = p.product_id
  LEFT JOIN product_discounts d ON d.discount_id = oi.discount_id AND d.active = 1
  WHERE o.order_date >= DATEADD(month, -3, GETDATE())
  GROUP BY o.customer_id
)
SELECT ca.country, ca.city, ca.street, ca.postal_code, co.OrderCount, co.OrderValue
FROM customer_addresses ca
INNER JOIN customers c ON ca.address_id = c.address_id
INNER JOIN CustomerOrders co ON c.customer_id = co.customer_id
WHERE co.OrderValue > 1000;
```

---
9. Znajdź sklepy, które nie sprzedały produktów w ciągu ostatnich 15 dni. Zwróć również datę ostatniego zamówienia dla każdego sklepu.

![Picture](images/images_results/30809.png)

```sql
WITH StoreLastOrder AS (
  SELECT store_id, MAX(order_date) AS LastOrderDate
  FROM orders
  GROUP BY store_id
)
SELECT s.store_name, s.country, s.city, s.street, s.postal_code, slo.LastOrderDate
FROM stores s
LEFT JOIN StoreLastOrder slo ON s.store_id = slo.store_id
WHERE slo.LastOrderDate < DATEADD(day, -15, GETDATE()) OR slo.LastOrderDate IS NULL;
```

---
10. Znajdź pracowników, którzy pracują w sklepie, w którym sprzedano najwięcej produktów w ciągu ostatnich 3 miesięcy. Zwróć również liczbę sprzedanych produktów i łączną wartość sprzedaży dla każdego pracownika. Nie uwzględniaj `discount` dla uproszczenia.

![Picture](images/images_results/30810.png)

```sql
WITH TopStore AS (
  SELECT TOP 1 store_id, COUNT(*) AS ProductCount, SUM(oi.quantity * p.price) AS SalesValue
  FROM orders o
  INNER JOIN order_items oi ON o.order_id = oi.order_id
  INNER JOIN products p ON oi.product_id = p.product_id
  WHERE o.order_date >= DATEADD(month, -3, GETDATE())
  GROUP BY store_id
  ORDER BY ProductCount DESC
), EmployeeSales AS (
  SELECT o.employee_id, COUNT(*) AS ProductCount, SUM(oi.quantity * p.price) AS SalesValue
  FROM orders o
  INNER JOIN order_items oi ON o.order_id = oi.order_id
  INNER JOIN products p ON oi.product_id = p.product_id
  WHERE o.store_id = (SELECT store_id FROM TopStore)
  GROUP BY o.employee_id
)
SELECT e.first_name, e.last_name, es.ProductCount, es.SalesValue
FROM employees e
INNER JOIN EmployeeSales es ON e.employee_id = es.employee_id;
```

---
11. Znajdź sumę sprzedaży dla każdej kategorii produktów w ostatnim roku i zapisz wynik do tabeli tymczasowej. Następnie wyświetl kategorie produktów, których sprzedaż przekroczyła 1000000 $.

![Picture](images/images_results/30811.png)

```sql
CREATE TABLE #SalesByCategory (
  category_id INT,
  category_name VARCHAR(255),
  sales DECIMAL(18,2)
)

INSERT INTO #SalesByCategory
SELECT p.category_id, c.category_name, SUM(oi.quantity * p.price) AS Sales
FROM orders o
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id
INNER JOIN categories c ON p.category_id = c.category_id
WHERE o.order_date >= DATEADD(year, -1, GETDATE())
GROUP BY p.category_id, c.category_name;

SELECT category_name, sales
FROM #SalesByCategory
WHERE sales > 1000000;
```

---
12. Znajdź zamówienia, które zostały złożone przez klientów mieszkających w Polsce, i przypisz im kategorię "Krajowy". Wynik zapisz do tabeli tymczasowej i wyświetl jej zawartość.

![Picture](images/images_results/30812.png)

```sql
CREATE TABLE #DomesticOrders (
  order_id INT,
  customer_id INT,
  order_status tinyint,
  order_date DATETIME,
  shipped_date DATETIME,
  domestic_category VARCHAR(50)
);

INSERT INTO #DomesticOrders
SELECT o.order_id, o.customer_id, o.order_status, o.order_date, o.shipped_date, 'Krajowy'
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN customer_addresses ca ON c.address_id = ca.address_id
WHERE ca.country = 'Poland';

SELECT * FROM #DomesticOrders;
```
