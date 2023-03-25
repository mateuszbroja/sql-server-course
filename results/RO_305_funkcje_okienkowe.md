# 305. Funkcje okienkowe - rozwiązania

1. Wyświetl nazwę produktu, cenę i numer wiersza dla każdego produktu w kategorii, z numerem wiersza posortowanym według ceny produktu.

![Picture](30501.png)

```
select category_name, product_name, price, rank() over(partition by category_name order by price) from products p 
join categories c on p.category_id = c.category_id;

select category_name, product_name, price, row_number() over(partition by category_name order by price) from products p 
join categories c on p.category_id = c.category_id;

select category_name, product_name, price, dense_rank() over(partition by category_name order by price) 
from products p 
join categories c on p.category_id = c.category_id;
```

```sql
SELECT
	product_name,
	price,
	ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY price) as row_num
FROM products;
```

---

2. Wyświetl nazwę produktu, cenę i rangę produktu w kategorii, sortując po cenie produktu. W przypadku remisu, produkty powinny otrzymać tę samą rangę.

![Picture](30502.png)

```sql
SELECT
	product_name,
	price,
	DENSE_RANK() OVER (PARTITION BY category_id ORDER BY price) as rank
FROM products;
```

---

3. Wyświetl nazwę produktu, ile było sprzedanych tych produktów, ich kategorię oraz ilość sprzedanych produktów w każdej z kategorii.

![Picture](30503.png)

```sql
SELECT DISTINCT
    products.product_name,
	SUM(order_items.quantity) OVER(PARTITION BY products.product_name) AS sold_products,
    categories.category_name,
    SUM(order_items.quantity) OVER(PARTITION BY categories.category_name) AS sold_by_category
FROM products
JOIN order_items
    ON products.product_id = order_items.product_id
JOIN categories
    ON products.category_id = categories.category_id;
```

---

4. Podziel produkty na 4 koszyki cenowe. Wyświetl nazwę produktu, cenę oraz numer koszyka (od 1 do 4). Posortuj rosnąco po cenie.

![Picture](30504.png)

```sql
SELECT
	product_name,
	price,
	NTILE(4) OVER (ORDER BY price) as price_group
FROM products
ORDER BY price;
```

---

5. Wyświetl nazwę produktu, cenę oraz wartość, która zwróci cenę produktu z poprzedniego wiersza w ramach danej kategorii.

![Picture](30505.png)

```sql
SELECT
	product_name,
	price,
	LAG(price) OVER (PARTITION BY category_id ORDER BY product_id) as prev_price
FROM products;
```

---

6. Wyświetl nazwę produktu, cenę oraz wartość, która zwróci cenę produktu z następnego wiersza w ramach danej kategorii.

![Picture](30506.png)

```sql
SELECT
	product_name,
	price,
	LEAD(price) OVER (PARTITION BY category_id ORDER BY product_id) as next_price
FROM products;
```

---

7. Wyświetl nazwę produktu, cenę oraz wartość, która zwróci pierwszą cenę produktu w ramach danej kategorii oraz wartość, która zwróci ostatnią cenę w danej kategorii. Pomiędzy tymi wartościami zwróć również kolumnę która pokaże średnią cenę w danej kategorii.

![Picture](30507.png)

```sql
SELECT
	product_name,
	price,
	FIRST_VALUE(price) OVER (PARTITION BY category_id ORDER BY price) as first_price, TODO zmien polecenie
	AVG(price) OVER (PARTITION BY category_id) as avg_price,
	LAST_VALUE(price) OVER (PARTITION BY category_id ORDER BY price) as last_price
FROM products;
```

---

8. Wyświetl klientów, kraj z jakiego pochodzą, ile złożyli zamówień, ile założono zamówień z kraju którego pochodzą oraz jaki procent stanowią ich zamówienia do zamówień z ich kraju.

![Picture](30508.png)

```sql
SELECT DISTINCT
    c.customer_id,
    c.first_name,
    c.last_name,
	ca.country,
	COUNT(o.order_id) OVER (PARTITION BY c.customer_id) AS num_orders,
	COUNT(o.order_id) OVER (PARTITION BY ca.country) AS num_local_orders,
    COUNT(o.order_id) OVER (PARTITION BY c.customer_id) * 100 / COUNT(o.order_id) OVER (PARTITION BY ca.country) AS local_order_percentage
FROM customers c
JOIN customer_addresses ca
	ON c.address_id = ca.address_id
JOIN orders o
	ON c.customer_id = o.customer_id
ORDER BY c.customer_id;
```
