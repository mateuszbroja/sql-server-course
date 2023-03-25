# 301. Podstawowe zapytania - rozwiązania

1. Wyświetl dane wszystkich klientów.

```sql
SELECT *
FROM sales.customers;
```

2. Wyświetl wszystkie produkty, które kosztują więcej niż 1000 $.

```sql
SELECT *
FROM sales.products
WHERE price > 1000;
```

3. Wyświetl wszystkie zamówienia, które zostały złożone po dniu 23 marca 2016 roku.

```sql
SELECT *
FROM sales.orders
WHERE order_date > '2016-03-23';
```

4. Wyświetl produkty marki 'Apple' laptopy, które kosztują pomiędzy 600 $, a 1200 $.

```sql
SELECT *
FROM sales.products
WHERE (price BETWEEN 600 AND 1000) AND brand = 'Apple';
```

5. Dla każdego z produktów wyświetl aktualną cenę oraz cenę po podwyżce o 100 $. Zamień nazwę obu kolumn na `aktualna_cena` oraz `cena_po_podwyzce`.

```sql
SELECT product_name, price as 'aktualna_cena', price + 100 as 'cena_po_podwyzce'
FROM sales.products;
```

6. Wyświetl dane wszystkich produktów, które po podwyżce 15% będą kosztować więcej niż 6500 $.

```sql
SELECT *, price * 1.15 as 'cena_po_podwyzce'
FROM sales.products
WHERE price * 1.15 > 6500;
```

7. Wyświetl dane z tabeli `sales.categories`, które mają nazwę kategorii 'Laptops', 'Smartphones' lub 'Speakers'.

```sql
SELECT *
FROM sales.categories
WHERE category_name IN ('Laptops', 'Smartphones', 'Speakers');
```

8. Znajdź wszystkich klientów, których nazwiska kończą się na literę 'n'. Posortuj ich alfabetycznie po imieniu.

```sql
SELECT *
FROM sales.customers
WHERE last_name LIKE '%n'
ORDER BY first_name;
```

9. Znajdź trzy produkty o najwyższej cenie.

```sql
SELECT TOP(3) *
FROM sales.products
ORDER BY price DESC;
```

10. Wyświetl dane 10% ze wszystkich produktów, które kosztują najmniej.

```sql
SELECT TOP(10) PERCENT *
FROM sales.products
ORDER BY price;
```

11. Wyświetl wszystkie unikalne nazwiska klientów.

```sql
SELECT DISTINCT last_name
FROM sales.customers;
```

12. Wyświetl wszystkich klientów, którzy nie mają numeru telefonu.

```sql
SELECT *
FROM sales.customers
WHERE phone_number IS NULL;
```

13. Dla każdego klienta wyświetl jego pierwsze imię i nazwisko, ale wyświetl je w jednej kolumnie.

```sql
SELECT first_name + ' ' + last_name as full_name
FROM sales.customers;
```
