# 310. Test praktyczny - rozwiązania

1. Wyświetl miasta, w których są klienci, ale w których nie ma sklepów.
2. Znajdź wszystkich klientów, których nazwiska kończą się na literę **`n`** oraz mają trzecią literę imienia **`m`**. Posortuj ich alfabetycznie po imieniu.

```sql
SELECT *
FROM customers
WHERE last_name LIKE '%n' AND first_name LIKE '__m%'
ORDER BY first_name;
```

3. Dla każdego pracownika wyświetl ile dni temu został zatrudniony. Dodatkowo wyświetl kolumnę, która pokaże, czy pracownik jest nadal zatrudniony, czy nie.

```sql
SELECT 
    employee_id, 
    first_name, 
    last_name, 
    DATEDIFF(day, start_date, GETDATE()) AS days_since_hire,
    CASE
        WHEN end_date IS NULL THEN 'pracuje'
        ELSE 'nie pracuje'
    END AS employment_status
FROM 
    employees
```


4. Wypisz nazwę produktów oraz liczbę liter w nazwie dla tych produktów, których nazwa ma 50 lub więcej znaków.

```sql
SELECT product_name, LEN(product_name)
FROM products
WHERE LEN(product_name) >= 50;
```

5. Zwróć jaki był stosunek pomiędzy zamówieniami z obniżką, a tych bez obniżki.

```sql
SELECT
	SUM(CASE WHEN discount_id IS NOT NULL THEN 1 ELSE 0 END) AS orders_with_discount,
	SUM(CASE WHEN discount_id IS NULL THEN 1 ELSE 0 END) AS orders_without_discount,
	CAST(SUM(CASE WHEN discount_id IS NOT NULL THEN 1 ELSE 0 END) AS DECIMAL) / CAST(SUM(CASE WHEN discount_id IS NULL THEN 1 ELSE 0 END) AS DECIMAL) AS discount_ratio
FROM order_items;
```

6. Pokaż ile mniej wydał klient, który wydał najmniej pieniędzy w stosunku do tego, który wydał najwięcej. Pomiń discount.

```sql
SELECT MAX(total_amount) - MIN(total_amount) AS difference
FROM (
    SELECT customer_id, SUM(price * quantity) AS total_amount
    FROM orders
    JOIN order_items ON orders.order_id = order_items.order_id
    JOIN products ON order_items.product_id = products.product_id
    GROUP BY customer_id
) AS amounts
WHERE customer_id IN (
    SELECT TOP 2 customer_id
    FROM (
        SELECT customer_id, SUM(price * quantity) AS total_amount
        FROM orders
        JOIN order_items ON orders.order_id = order_items.order_id
        JOIN products ON order_items.product_id = products.product_id
        GROUP BY customer_id
    ) AS amounts
    ORDER BY total_amount
);
```

7.  Wyświetl produkty, których ceny są mniejsze niż 300 $ lub większe niż 2000 $.

8. Pokaż rekordy które są zduplikowane

```sql
SELECT product_name, COUNT(*) AS num_duplicates
FROM products
GROUP BY product_name
HAVING COUNT(*) > 1;
```

9. Pokaż zamówienia, w których znajduje się przynajmniej 51 rzeczy (nie pozycji w zamówieniu) oraz wyklucz te zamówienia, które zostały zamówione przez pracowników, którzy już nie pracują.

```sql
SELECT o.order_id, o.order_date
FROM orders o
INNER JOIN (
SELECT order_id, SUM(quantity) as total_quantity
FROM order_items
GROUP BY order_id
HAVING SUM(quantity) > 50
) sq ON o.order_id = sq.order_id
LEFT JOIN employees e ON o.employee_id = e.employee_id
WHERE e.end_date IS NULL
```

10.  Wyświetl pracowników, którzy mogą być również klientami sklepu. Zweryfikuj to po imieniu i nazwisku.

11.  Wyświetl wszystkich klientów, którzy złożyli zamówienia w 2017 roku.

12. Dla każdego miesiąca roku podaj wartość sprzedaży (ma być 12 rekordów w tabeli wynikowej).

13. Pokaż produkty, które zostały zamówione mniej niż 1100 razy.

14. Pokaż ile zajmuje wysłanie zamówienia przez poszczególne sklepy.

15. Wyświetl 10% produktów, które kosztują najwięcej.

16. Wyświetl nazwę produktu, ile było sprzedanych tych produktów, ich kategorię oraz ilość sprzedanych produktów w każdej z kategorii.

17. Wyświetl nazwę produktu, ile było sprzedanych tych produktów, ich kategorię oraz ilość sprzedanych produktów w każdej z kategorii.

18. Wyświetl wszystkie występujące imiona i nazwiska pracowników w jednej kolumnie o nazwie **`first_last_name`**.

19. Wyznacz średnią wartość zamówień w roku dla każdego klienta, który dokonał co najmniej jednego zamówienia w każdym roku, w którym zostało dokonane jakiekolwiek zamówienie.

```sql
SELECT 
    customer_id, 
    AVG(order_total) AS average_order_value
FROM (
    SELECT 
        customer_id, 
        SUM(price * quantity) AS order_total, 
        YEAR(order_date) AS order_year,
        COUNT(*) OVER (PARTITION BY customer_id, YEAR(order_date)) AS orders_in_year
    FROM 
        orders 
        JOIN order_items ON orders.order_id = order_items.order_id 
        JOIN products ON order_items.product_id = products.product_id
    WHERE 
        order_status = 1
	GROUP BY customer_id, YEAR(order_date)
) AS order_summary
WHERE 
    orders_in_year > 0
GROUP BY 
    customer_id
HAVING 
    COUNT(*) = (SELECT COUNT(DISTINCT YEAR(order_date)) FROM orders)
```


20. Zwróć imię i nazwisko pracownika oraz sumę ilości sprzedanych produktów w 2022 oraz w 2023 roku (w osobnych kolumnach). W przypadku braku sprzedaży w danym roku, w kolumnie wyświetl **`0`**. 

```sql
SELECT 
    e.first_name, 
    e.last_name, 
    COALESCE(SUM(oi.quantity), 0) AS total_sales,
    COALESCE(SUM(CASE WHEN YEAR(o.order_date) = 2022 THEN oi.quantity ELSE 0 END), 0) AS sales_2022,
    COALESCE(SUM(CASE WHEN YEAR(o.order_date) = 2023 THEN oi.quantity ELSE 0 END), 0) AS sales_2023
FROM 
    employees e 
    LEFT JOIN orders o ON e.employee_id = o.employee_id
    LEFT JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY 
    e.first_name, 
    e.last_name;
```

21. Zapytanie to wybiera nazwę produktu, cenę oraz ranga ceny produktu, wyznaczona za pomocą funkcji `RANK()` oraz `DENSE_RANK()`. Wyniki są sortowane malejąco po cenie produktu.

```sql
SELECT 
    product_name, 
    price, 
    RANK() OVER (ORDER BY price DESC) AS price_rank, 
    DENSE_RANK() OVER (ORDER BY price DESC) AS price_dense_rank
FROM 
    products;
```

22. Wyświetl nazwę produktu, cenę, nazwę sklepu oraz ilość sprzedanych sztuk dla TOP 5 najlepiej sprzedających się produktów w 2022. Dla każdego z produktu pokaż podział na poszczególne sklepy. Ostatecznie ma się pojawić **`5 x ilość sklepów`** rekordów.

```sql
WITH top_selling_products AS (
    SELECT TOP 5
        product_id,
        SUM(quantity) AS total_quantity
    FROM 
        order_items
    GROUP BY 
        product_id
    ORDER BY 
        total_quantity DESC
)
SELECT 
    p.product_name, 
    p.price,
    s.store_name,
    COALESCE(SUM(oi.quantity), 0) AS total_sales
FROM 
    top_selling_products tp 
    JOIN products p ON tp.product_id = p.product_id
    JOIN order_items oi ON tp.product_id = oi.product_id
    JOIN orders o ON oi.order_id = o.order_id
    JOIN stores s ON o.store_id = s.store_id
WHERE 
    YEAR(o.order_date) = 2022
GROUP BY 
    p.product_name, 
    p.price,
    s.store_name;
```

23. Dla każdej kategorii policz liczbę produktów kosztujących więcej niż 1500 $. Jeżeli kategoria nie posiada żadnych produktów droższych niż 1500 $, to i tak pokaż kategorię z liczbą **`0`**.

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