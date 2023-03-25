# 307. Podzapytania - rozwiązania

1. Wyświetl produkty, które kosztują więcej niż średnia wszystkich produktów.

![Picture](images/images_results/30701.png)

```sql
SELECT *
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

---
2. Zwróć nazwę produktu oraz jego cenę. Lista powinna zawierać tylko produkty, które posiadają cenę większą niż jakakolwiek średnia cena produktów w danym brandzie. Posegreguj wyniki po cenie, od największej do najmniejszej.

![Picture](images/images_results/30702.png)

```sql
SELECT
    product_name,
    price
FROM products
WHERE price > ANY (
        SELECT AVG(price)
        FROM products
        GROUP BY brand
    )
ORDER BY price DESC;
```

---
3. Zwróć nazwę produktu oraz jego cenę. Lista powinna zawierać tylko produkty, które posiadają cenę większą niż wszystkie średnie ceny produktów w danym brandzie. Posegreguj wyniki po cenie, od największej do najmniejszej.

![Picture](images/images_results/30703.png)

```sql
SELECT
    product_name,
    price
FROM products
WHERE price > ALL
	(
        SELECT AVG(price)
        FROM products
        GROUP BY brand
    )
ORDER BY price DESC;
```

---
4. Znajdź klientów, którzy nie kupili nic w **`2013`** roku, bez użycia **`JOIN`**. Przedstaw dwa sposoby na utrzymanie wyniku (użyj **`IN`** oraz **`EXISTS`**).

![Picture](images/images_results/30704.png)

```sql
SELECT
    customer_id,
    first_name,
    last_name
FROM customers c
WHERE NOT EXISTS
(
		SELECT customer_id
        FROM orders o
        WHERE o.customer_id = c.customer_id
        AND YEAR (order_date) = 2012
)
ORDER BY
    first_name,
    last_name;
```

```sql
SELECT
    customer_id,
    first_name,
    last_name
FROM customers c
WHERE customer_id NOT IN
(
		SELECT customer_id
        FROM orders o
        WHERE YEAR (order_date) = 2012
)
ORDER BY
    first_name,
    last_name;
```

---
5. Wyświetl id klientów, którzy mieszkają w tym samym mieście, co jest sklep. Masz do dyspozycji tylko jeden **`JOIN`**.

![Picture](images/images_results/30705.png)

```sql
SELECT DISTINCT c.customer_id
FROM customers c
JOIN customer_addresses ca ON c.address_id = ca.address_id
WHERE ca.city IN
(
  SELECT s.city
  FROM stores s
);
```

---
6. Zwróć średnią ilość zamówień jaką przyjmuje pracownik. Nie używaj **JOIN**.

![Picture](images/images_results/30706.png)

```sql
SELECT AVG(order_count) average_order_count_by_employee
FROM
(
    SELECT 
		employee_id, 
        COUNT(order_id) order_count
    FROM orders
    GROUP BY employee_id
) t;
```

---
7. Wyświetl produkty, które były zamówione więcej niż 10 razy w jednym zamówieniu. Nie używaj **`JOIN`**.

![Picture](images/images_results/30707.png)

```sql
SELECT product_name
FROM products
WHERE product_id = ANY (
    SELECT product_id
    FROM order_items
    WHERE quantity > 10
)
ORDER BY product_name;
```


---
8. Wyświetl dane wszystkich pracowników, którzy zarabiają o minimum 30% mniej niż jest średnia wszystkich pensji w firmie.

![Picture](images/images_results/30708.png)

```sql
SELECT *
FROM employees
WHERE salary <= (SELECT AVG(salary) FROM employees)*0.7;
```


---
9. Wyświetl liczbę wszystkich pracowników oraz liczbę pracowników, którzy już nie pracują. Nie używaj JOIN, ani UNION.

![Picture](images/images_results/30709.png)

```sql
SELECT
(SELECT COUNT(*) FROM employees) as 'Employees count',
(SELECT COUNT(*) FROM employees WHERE end_date IS NOT NULL) as 'Ex-Employees count'
```

---
10. Wyświetl pracowników, którzy mają nazwiska w TOP 3 najdłuższych nazwisk.

![Picture](images/images_results/30710.png)

```sql
SELECT *
FROM employees
WHERE LEN(last_name) = ANY (
SELECT DISTINCT TOP 3 MAX(LEN(last_name))
FROM employees
GROUP BY last_name
ORDER BY MAX(LEN(last_name)) DESC
);
```

---
11. Wyświetl pracowników, ich stawkę oraz pozycję, którzy zarabiają mniej niż wynosi średnia zarobków na ich pozycji.

![Picture](images/images_results/30711.png)

```sql
SELECT e.first_name, e.last_name, e.salary, e.position
FROM employees e
WHERE e.salary < (SELECT AVG(e2.salary) FROM employees e2 WHERE e2.position = e.position);
```

---
12. Wyświetl dane pracowników, którzy pracują najdłużej.

![Picture](images/images_results/307012.png)

```sql
SELECT *
FROM employees
WHERE start_date = (SELECT MIN(start_date) FROM employees);
```

---
13. Wyświetl klientów oraz ich procentowy udział zamówień do liczby wszystkich zamówień.

![Picture](images/images_results/30713.png)

```sql
SELECT 
    c.first_name, 
    c.last_name, 
    COUNT(o.order_id) as order_count, 
    (COUNT(o.order_id) * 100.0 / (SELECT COUNT(*) FROM orders)) as orders_procentage
FROM 
    customers c, 
    orders o
WHERE 
    c.customer_id = o.customer_id
GROUP BY 
    c.first_name, 
    c.last_name
```


---
14. Dla każdego produktu wyświetl różnicę jego ceny do średniej ceny produktów w tej samej kategorii.

![Picture](images/images_results/30714.png)

```sql
SELECT
	p.product_name,
	p.price,
	(p.price - (
		SELECT AVG(p2.price)
		FROM products p2
		WHERE p2.category_id = p.category_id)) as roznica_ceny_do_sredniej
FROM products p
```

---
15. Wyświetl jaka jest maksymalna liczba wszystkich zamówionych rzeczy z jednego zamówienia.

![Picture](images/images_results/30715.png)

```sql
SELECT MAX(cnt) as max_ordered_items
FROM (
    SELECT SUM(quantity) as cnt
    FROM order_items oi 
    JOIN orders o ON oi.order_id = o.order_id
    GROUP BY o.order_id
) t
```

---
16. Dla każdej kategorii wyświetl kiedy po raz pierwszy został zamówiony produkt z tej kategorii.

![Picture](images/images_results/30716.png)

```sql
SELECT category_name, min_date
FROM categories
LEFT JOIN (
	SELECT
		MIN(order_date) as min_date,
		p.category_id
	FROM orders o
	INNER JOIN order_items oi ON oi.order_id = o.order_id
	INNER JOIN products p ON p.product_id = oi.product_id
	GROUP BY p.category_id
	) as t
ON t.category_id = categories.category_id
```

---
17. Wyświetl pracowników, których pensja jest pomiędzy najmniejszą pensją w firmie, a 28 000 $.

![Picture](images/images_results/30717.png)

```sql
SELECT * 
FROM employees 
WHERE salary BETWEEN (
	SELECT MIN(salary) 
	FROM employees) AND 28000;
```

---
18. Wyświetl pracowników, którzy dostają drugą największą wypłatę w firmie.

![Picture](images/images_results/30718.png)

```sql
SELECT 
    e.employee_id, 
    e.first_name, 
    e.last_name, 
    e.salary
FROM employees e
WHERE e.salary = (
        SELECT MAX(e2.salary)
        FROM employees e2
        WHERE 
            e2.salary < (
                SELECT MAX(e3.salary)
                FROM employees e3))
```

---
19. Zwróć wszystkie produkty, które są z tej samej firmy, co produkt **`GeForce RTX 3060`**. Nie używaj JOIN.

![Picture](images/images_results/30719.png)

```sql
SELECT 
    p.product_name,
    p.brand
FROM products p
WHERE p.brand = (
        SELECT p2.brand 
        FROM products p2 
        WHERE p2.product_name = 'GeForce RTX 3060')
```

---
20. Znajdź pracowników, którzy zarabiają więcej niż najmniej zarabiający menadżer.

![Picture](images/images_results/30720.png)

```sql
SELECT 
    e.employee_id,
    e.first_name,
    e.last_name,
    e.salary
FROM employees e
WHERE e.salary > (
        SELECT MIN(m.salary)
        FROM employees m
        WHERE m.position = 'Manager'
    )
```

---
21. Wyświetl pracowników, których pensja jest wyższa niż średnia pensja w z każdej z pozycji.

![Picture](images/images_results/30721.png)

```sql
SELECT 
    e.employee_id, 
    e.first_name, 
    e.last_name, 
    e.salary, 
    e.position
FROM employees e
WHERE 
    e.salary > (
        SELECT AVG(e2.salary)
        FROM employees e2
        WHERE e2.position = e.position)
```

---
22. Dla każdego produktu wyświetl kategorię oraz liczbę produktów z tą samą kategorią. Nie używaj JOIN.

![Picture](images/images_results/30722.png)

```sql
SELECT 
    p.product_name, 
    (SELECT category_name FROM categories WHERE category_id = p.category_id) as category_name, 
    (SELECT COUNT(*) FROM products WHERE category_id = p.category_id) as liczba_produktow
FROM 
    products p
```
