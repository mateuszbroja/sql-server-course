# 302. Funkcje wbudowane - rozwiązania

1. Dla każdego zamówienia wyświetl `order_id` oraz rok, w którym zamówienie zostało złożone (`order_date`) i podpisz je jako _order_year_.

```sql
SELECT order_id, YEAR(order_date) as 'order_year'
FROM sales.orders
```

2. Dla każdego zamówienia wyświetl order_date oraz dzień tygodnia w którym zostało złożone (order_date). Wyświetl tylko aktywne zamówienia.

3. Wyświetl wszystkich pracowników urodzonych w 1990 roku. Napisz podane zapytania na 3 różne sposoby.

```sql
SELECT *
FROM Employee
WHERE YEAR(BirthDate) = 1990
SELECT *
FROM Employee
WHERE BirthDate >= '1990-1-1'
AND BirthDate < '1991-1-1'
```

4. Wyświetl bieżącą datę i godzinę.

```sql
SELECT GETDATE() as 'Teraz'
```

5. Wyświetl, ile lat minęło od daty 1 stycznia 1990 roku.

```sql
SELECT DATEDIFF(year, '1990-01-01', GETDATE()) as IleLat
```

6. Dla każdego zamówienia wyświetl `order_id` oraz ile dni temu zostało zamówione.

```sql
SELECT
FirstName,
LastName,
DATEDIFF(yy, BirthDate, GETDATE()) as Wiek
FROM Employee
```

7. Dla każdego pracownika wyświetl imię, nazwisko oraz inicjały.

```sql
SELECT
FirstName as 'Imię',
LastName as 'Nazwisko',
SUBSTRING(FirstName, 1, 1) as 'Pierwsza litera imienia',
SUBSTRING(LastName, 1, 1) as 'Pierwsza litera nazwiska'
FROM Employee
```

8. Wypisz wszystkie zamówienia, które zostały złożone w styczniu lub w lutym.

```sql
SELECT *
FROM Company
WHERE MONTH(StartDate) IN (1,2)
AND Active = 1
```

9. Wypisz nazwę oraz liczbę liter w nazwie dla tych produktów, których nazwa ma 7 lub więcej znaków.

```sql
SELECT Name, LEN(Name) as LiczbaLiter
FROM Company
WHERE LEN(Name) >= 8
```

10. Wyświetl nazwy oraz długość tych nazw dla dwóch produktów, których nazwy są najdłuższe. Jeśli istnieje więcej produktów, które mają długość nazwy taką samą, jak tego produktu na drugim miejscu, również je uwzględnij.

```sql
SELECT TOP 2 WITH TIES Name, LEN(Name) as Dlugosc
FROM Company
ORDER BY LEN(Name) DESC
```

11. Wyświetl wszystkie unikalne wartości długości połączonego imienia i nazwiska (razem z odstępem) klientów. Wyniki posortuj rosnąco według tych wartości.

```sql
SELECT DISTINCT LEN(Name) as Dlugosc
FROM Company
ORDER BY LEN(Name)
```

12. Wyświetl produkty, które zostały już wysłane i dodaj kolumnę, która pokazuje ile dni zajęło pomiędzy złożeniem zamówienia, a wysłaniem produktów.
