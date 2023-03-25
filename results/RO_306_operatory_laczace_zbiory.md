# 306. Operatory łączące zbiory - rozwiązania

1. Pokaż imiona i nazwiska wszystkich klientów oraz pracowników. Posegreguj tę listę alfabetycznie po nazwisku.

![Picture](30601.png)

```sql
	SELECT
	    first_name,
	    last_name
	FROM employees
UNION ALL
	SELECT
	    first_name,
	    last_name
	FROM customers
	ORDER BY last_name;
```

---

2. Pokaż kraj oraz miasto, z których pochodzą klienci, ale nie ma tam sklepów. Zapytanie wykonaj na dwa sposoby, jedno z użyciem operatora do zbiorów, a drugi z użyciem JOIN.

Rozwiązanie posiada 59 rekordów.

![Picture](30602.png)

```sql
SELECT DISTINCT c.country, c.city
FROM customer_addresses c
LEFT JOIN stores s ON c.country = s.country AND c.city = s.city
WHERE s.country IS NULL OR s.city IS NULL;
```

```sql
	SELECT country, city
	FROM customer_addresses
EXCEPT
	SELECT country, city
	FROM stores;
```

---

3. Pokaż miasta z których są klienci oraz są zlokalizowane sklepy. Ponownie, użyj dwóch sposobów, aby to osiągnąć.

Rozwiązanie posiada 10 rekordów.

![Picture](30603.png)

```sql
SELECT DISTINCT c.city
FROM customer_addresses c
JOIN stores s ON c.city = s.city;
```

```sql
	SELECT city
	FROM customer_addresses
INTERSECT
	SELECT city
	FROM stores;
```

---

4. Wyświetl produkty, których ceny są mniejsze niż 200 $ lub większe niż 3000 $.

Rozwiązanie posiada 55 rekordów.

![Picture](30604.png)

```sql
	SELECT product_name
	FROM Products
	WHERE Price < 200
UNION
	SELECT product_name
	FROM Products
	WHERE Price > 3000;
```

---

5. Wyświetl wszystkie występujące imiona i nazwiska pracowników w jednej kolumnie o nazwie **`first_last_name`**.

Rozwiązanie posiada 64 rekordów.

![Picture](30605.png)

```sql
	SELECT first_name AS 'first_last_name'
	FROM employees
UNION ALL
	SELECT last_name
	FROM employees
```

---

6. Pokaż liczbę produktów, które wpadają w koszyk z ceną mniejszą lub równą 1000 $ oraz w koszyk z ceną większą niż 1000 $. Nazwij kolumny jak w rozwiązaniu poniżej.

![Picture](30606.png)

```sql
	SELECT
		COUNT(*) AS count,
		'Less or equal than 1000 $' AS product_basket
	FROM products
	WHERE price <= 1000
UNION ALL
	SELECT
		COUNT(*) AS count,
		'More than 1000 $' AS product_basket
	FROM products
	WHERE price > 1000
```

---

7. Pokaż zamówienia, w których znajduje się produkt kupiony powyżej 9 razy, ale wyklucz te, które zostały procesowane przez pracownik, który już nie pracuje.

Rozwiązanie posiada 2654 rekordów.

![Picture](30607.png)

```sql
	SELECT DISTINCT o.order_id
	FROM orders o
	INNER JOIN order_items oi
		ON o.order_id = oi.order_id
	WHERE oi.quantity > 9
EXCEPT
	SELECT o.order_id
	FROM orders o
	INNER JOIN employees e
		ON o.employee_id = e.employee_id
	WHERE e.end_date IS NOT NULL;
```
