# 304. Funkcje agregujące - rozwiązania

1. Policz wszystkich klientów.

![Picture](30401.png)

```sql
SELECT
	COUNT(*) AS 'customers_count'
FROM customers;
```

---

2. Policz liczbę numerów telefonów z tabeli **`clients`**. Dlaczego ich liczba różni się od liczby klientów?

![Picture](30402.png)

```sql
SELECT
	COUNT(phone_number) AS phone_count
FROM customers;
```

```yaml
Odpowiedź: "ponieważ COUNT() nie liczy wartości NULL"
```

---

3. Policz wszystkich klientów, którzy nie mają numeru telefonu.

![Picture](30403.png)

```sql
SELECT
	COUNT(*) AS phoneless_customers_count
FROM customers
WHERE phone_number IS NULL;
```

---

4. Wyświetl wszystkie unikalne ilości zamówionych produktów. Posegreguj wyniki od największej liczby do najmniejszej.

![Picture](30404.png)

```sql
SELECT DISTINCT
	quantity
FROM order_items
ORDER BY quantity DESC;
```

---

5. Dla każdego roku podaj wartość sprzedaży. Posegreguj po kolumnie **`Year`**. Weź pod uwagę cenę, ilość oraz zniżkę.

![Picture](30405.png)

```sql
SELECT
	o.order_id,
	order_item_id, YEAR(o.order_date),
	p.price, quantity,
	d.discount,
	d.active,
	SUM(p.price * quantity * (1 - COALESCE(d.discount, 0)))
FROM orders o
JOIN order_items oi
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
LEFT JOIN product_discounts d
	ON d.discount_id = oi.discount_id AND d.active = 1
GROUP BY o.order_id, order_item_id, YEAR(o.order_date), p.price, quantity, d.discount, d.active
ORDER BY o.order_id, order_item_id;
```

```sql
SELECT
	YEAR(o.order_date) AS Year,
	SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) AS Sales_sum
FROM orders o
JOIN order_items oi
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
LEFT JOIN product_discounts d ON d.discount_id = oi.discount_id AND d.active = 1
GROUP BY YEAR(o.order_date)
ORDER BY YEAR(o.order_date);
```

---

6. Dla każdej kategorii policz liczbę produktów kosztujących więcej niż 1500 $. Jeżeli kategoria nie posiada żadnych produktów droższych niż 1500 $, to i tak pokaż kategorię z liczbą **`0`**.

![Picture](30406.png)

```sql
SELECT
	c.category_name,
COUNT(p.product_id) AS expensive_products_count
FROM categories c
LEFT JOIN products p
	ON c.category_id = p.category_id AND p.price > 1500
GROUP BY c.category_name
ORDER BY COUNT(p.product_id) DESC;
```

---

7. Pokaż produkty, które zostały zamówione mniej niż 1100 razy. Posortuj je od najmniejszej liczby zamówień do największej.

![Picture](30407.png)

```sql
SELECT
	p.product_name,
	SUM(quantity) AS order_count
FROM products p
LEFT JOIN order_items oi
	ON p.product_id = oi.product_id
LEFT JOIN orders o
	ON oi.order_id = o.order_id
GROUP BY p.product_name
HAVING SUM(quantity) < 1100
ORDER BY COUNT(o.order_id);
```

---

8. Pokaż ile kosztowało każde z poszczególnych zamówień. Wyświetl order_id oraz wartość zamówienia pod nazwą `order_price`. Uwzględnij cenę produktu, jego ilość oraz zniżkę (o ile jest). Posegreguj zamówienia od największej do najmniejszej wartości ceny.

![Picture](30408.png)

```sql
SELECT
	o.order_id,
	SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) AS order_price
FROM orders o
JOIN order_items oi
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
LEFT JOIN product_discounts d
	ON d.discount_id = oi.discount_id AND d.active = 1
GROUP BY o.order_id
ORDER BY SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) DESC;
```

---

9. Pokaż to samo co w poprzednim zadaniu, ale ogranicz wyniki do sklepów z Polski oraz order_price nie mniejsze niż 3000 $.

![Picture](30409.png)

```sql
SELECT
	o.order_id,
	SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) AS order_price
FROM orders o
JOIN order_items oi
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
LEFT JOIN product_discounts d
	ON d.discount_id = oi.discount_id AND d.active = 1
JOIN stores s
	ON o.store_id = s.store_id
WHERE s.country = 'Poland'
GROUP BY o.order_id
HAVING SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) > 3000
ORDER BY SUM(p.price * quantity * (1 - COALESCE(d.discount, 0)));
```

---

10. Pokaż dla każdej z kategorii średnią cenę produktów w tej kategorii. Wyświetl tylko te kategorię, których średnia cena produktów jest wyższa niż 1000 $.

![Picture](30410.png)

```sql
SELECT
	category_id,
	AVG(price) avg_price
FROM products
GROUP BY category_id
HAVING AVG(price) > 1000;
```

---

11. Wyświetl pracownika, który ma najkrótsze imię. Pokaż z ilu liter się składa.

![Picture](30411.png)

```sql
SELECT TOP 1
	first_name,
	LEN(first_name) AS name_length
FROM employees
ORDER BY name_length ASC;
```

---

12. Wyświetl klienta, który wydał największą ilość pieniędzy (nie uwzględniaj zniżki).
TODO dodaj discount
![Picture](30412.png)

```sql
SELECT TOP 1
	c.customer_id,
	c.first_name,
	c.last_name,
	SUM(p.price * oi.quantity) AS total_spent
FROM customers c
INNER JOIN orders o
	ON c.customer_id = o.customer_id
INNER JOIN order_items oi
	ON o.order_id = oi.order_id
INNER JOIN products p
	ON oi.product_id = p.product_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_spent DESC;
```

---

13. Podaj w jednej kolumnie liczbę liter w nazwisku klientów oraz w drugiej policz ilu jest klientów, którzy taka ilość mają. Np. 13 klientów ma nazwisko składające się z 3 liter, 24 klientów ma nazwisko składające się z 4 liter, itd.

![Picture](30413.png)

```sql
SELECT
	LEN(last_name) AS name_length,
	COUNT(*) AS count_of_customers
FROM customers
GROUP BY LEN(last_name)
ORDER BY name_length ASC;
```

---

14. Wyświetl ile jest średnio produktów na każdym z zamówień.

![Picture](30414.png)

```sql
SELECT
	AVG(quantity) AS avg_products_per_order
FROM order_items;
```

---

15. Wyświetl ile w każdym roku było zamówień z poszczególnych sklepów.

![Picture](30415.png)

```sql
SELECT
	YEAR(o.order_date) AS order_year,
	s.store_name,
	COUNT(*) AS order_count
FROM orders o
INNER JOIN stores s
	ON o.store_id = s.store_id
GROUP BY YEAR(o.order_date), s.store_name
ORDER BY order_year ASC, s.store_name ASC;
```

---

16. Wyświetl aktywnego pracownika, który pracuje najdłużej. Pokaż ile dni pracuje do dzisiaj.

![Picture](30416.png)

```sql
SELECT TOP 1
	first_name,
	last_name,
	DATEDIFF(day, start_date, GETDATE()) AS days_worked
FROM employees
WHERE end_date IS NULL
ORDER BY DATEDIFF(day, start_date, GETDATE()) DESC;
```

---

17. Sprawdź, ile średnio klienci zamawiają produktów i jaka jest największa kupiona ilość produktów w jednym zamówieniu.

![Picture](30417.png)

```sql
SELECT
	AVG(quantity) AS avg_quantity,
	MAX(quantity) AS max_quantity
FROM order_items;
```

---

18. Zwróć liczbę sprzedanych produktów oraz ich łączny zapas z tabeli `product_inventories`.

![Picture](30418.png)

```sql
SELECT
    order_items.product_id,
    SUM(order_items.quantity) AS quantity_sold,
    SUM(product_inventories.quantity) AS total_stock
FROM order_items
JOIN product_inventories
    ON order_items.product_id = product_inventories.product_id
GROUP BY order_items.product_id
ORDER BY SUM(order_items.quantity) DESC;
```

---

19. Policz średnią liczbę miesięcy przepracowanych przez tych pracowników, którzy już w firmie nie pracują.

![Picture](30419.png)

```sql
SELECT AVG(DATEDIFF(month, start_date, end_date)) AS avg_months_worked
FROM employees
WHERE end_date IS NOT NULL;
```

---

20. Pokaż 5 pracowników, którzy przyjęli najwięcej zamówień.

![Picture](30420.png)

```sql
SELECT TOP 5
    employees.employee_id,
    employees.first_name,
    employees.last_name,
    COUNT(orders.order_id) AS total_orders
FROM employees
INNER JOIN orders
	ON orders.employee_id = employees.employee_id
GROUP BY employees.employee_id, employees.first_name, employees.last_name
ORDER BY total_orders DESC;
```

---

21. Wyświetl kraje, w których znajduje się więcej niż jeden sklep.

![Picture](30421.png)

```sql
SELECT
	country
FROM stores
GROUP BY country
HAVING COUNT(store_id) > 1;
```

---

22. Policz wszystkie towary, których cena jest niższa niż 50 $.

![Picture](30422.png)

```sql
SELECT
	COUNT(*) AS num_products
FROM products
WHERE price < 50;
```

---

23. Pokaż ilu pracowników było zatrudnionych w każdym roku.

![Picture](30423.png)

```sql
SELECT
	YEAR(start_date) AS year,
	COUNT(*) AS num_employees
FROM employees
GROUP BY YEAR(start_date);
```

---

24. Wyświetl pracownika, który przyjął największą liczbę zamówień.

![Picture](30424.png)

```sql
SELECT TOP 1
	e.employee_id,
	e.first_name,
	e.last_name,
	COUNT(o.order_id) AS num_orders
FROM employees e
JOIN orders o
	ON e.employee_id = o.employee_id
GROUP BY e.employee_id, e.first_name, e.last_name
ORDER BY num_orders DESC;
```

---

25. Wyświetl kategorię produktów i ile wynosiła dla niej łączna kwota zamówień.

![Picture](30425.png)

```sql
SELECT
	c.category_name,
	SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) AS total_order_amount
FROM orders o
JOIN order_items oi
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
JOIN categories c
	ON p.category_id = c.category_id
LEFT JOIN product_discounts d
	ON d.discount_id = oi.discount_id AND d.active = 1
GROUP BY c.category_name
ORDER BY SUM(p.price * quantity * (1 - COALESCE(d.discount, 0))) DESC;
```

---

26. Dla każdego produktu, który był kiedykolwiek zamówiony, wyświetl datę, kiedy stało się to po raz pierwszy.

![Picture](30426.png)

```sql
SELECT
	p.product_id,
	p.product_name,
	MIN(o.order_date) AS first_order_date
FROM products p
JOIN order_items oi
	ON p.product_id = oi.product_id
JOIN orders o
	ON oi.order_id = o.order_id
GROUP BY p.product_id, p.product_name;
```

---

27. Dla każdego produktu policz, ile sztuk zostało zamówionych w 2015 roku. Wyniki ogranicz tylko do tych produktów, których zamówiono łącznie mniej niż 50.

![Picture](30427.png)

```sql
SELECT
	p.product_id,
	p.product_name,
	SUM(oi.quantity) AS total_quantity
FROM products p
JOIN order_items oi
	ON p.product_id = oi.product_id
JOIN orders o
	ON oi.order_id = o.order_id
WHERE o.order_date >= '2015-01-01' AND o.order_date <= '2015-12-31'
GROUP BY p.product_id, p.product_name
HAVING SUM(oi.quantity) < 50;
```

---

28. Wyświetl trzy grupy produktów, z których łącznie zamówiono największą liczbę produktów w 2015 roku.

![Picture](30428.png)

```sql
SELECT TOP 3
	c.category_name,
	SUM(oi.quantity) AS total_quantity
FROM order_items oi
JOIN orders o
	ON oi.order_id = o.order_id
JOIN products p
	ON oi.product_id = p.product_id
JOIN categories c
	ON p.category_id = c.category_id
WHERE o.order_date >= '2015-01-01' AND o.order_date <= '2015-12-31'
GROUP BY c.category_name
ORDER BY total_quantity DESC;
```

---

29. Wyświetl liczbę zamówień oraz łączną wartość zamówień, uwzględniając dni tygodnia, w których złożono dane zamówienie.

![Picture](30429.png)

```sql
SELECT
    DATEPART(WEEKDAY, o.order_date) AS order_day_of_week,
    COUNT(*) AS total_orders,
    SUM((oi.quantity * p.price) * (1 - COALESCE(pd.discount, 0))) AS total_order_value
FROM orders o
JOIN order_items oi
	ON o.order_id = oi.order_id
JOIN products p
	ON oi.product_id = p.product_id
LEFT JOIN product_discounts pd ON oi.discount_id = pd.discount_id
GROUP BY DATEPART(WEEKDAY, o.order_date)
ORDER BY DATEPART(WEEKDAY, o.order_date);
```

---

30. Wyświetl 5% towarów, których łącznie sprzedano najwięcej.

![Picture](30430.png)

```sql
SELECT TOP 5 PERCENT
	product_name,
	SUM(quantity) as Liczba
FROM products p
INNER JOIN order_items o
	ON p.product_id = o.product_id
GROUP BY product_name
ORDER BY SUM(quantity) DESC;
```

---

31. Wyświetl, ile łącznie zaoszczędzili klienci z zamówień towarów, które zostały sprzedane po cenie niższej niż cena sugerowana (zamówienia po obniżce).

![Picture](30431.png)

```sql
SELECT
    SUM((products.price - (products.price * product_discounts.discount)) * order_items.quantity) AS total_savings
FROM orders
INNER JOIN order_items
	ON orders.order_id = order_items.order_id
INNER JOIN products
	ON order_items.product_id = products.product_id
INNER JOIN product_discounts
	ON order_items.discount_id = product_discounts.discount_id
WHERE product_discounts.active = 1;
```

---

32. Wyświetl łączną wartość zamówień oraz to, jaka byłaby łączna wartość zamówień, gdyby wszystkie towary zostały sprzedane po cenie sugerowanej.

![Picture](30432.png)

```sql
SELECT
    SUM(products.price * order_items.quantity) AS actual_sales,
    SUM(products.price * order_items.quantity * (1 - COALESCE(product_discounts.discount, 0))) AS suggested_sales
FROM orders
INNER JOIN order_items
	ON orders.order_id = order_items.order_id
INNER JOIN products
	ON order_items.product_id = products.product_id
LEFT JOIN product_discounts
	ON order_items.discount_id = product_discounts.discount_id AND product_discounts.active = 1;
```
