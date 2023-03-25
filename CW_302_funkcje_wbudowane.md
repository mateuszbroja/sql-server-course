# 302. Funkcje wbudowane - ćwiczenia

```yaml
YEAR: zwracająca rok z podanej daty.
MONTH: zwracająca miesiąc z podanej daty.
DAY: zwracająca dzień z podanej daty.
DATEPART: zwracająca wybrany składnik daty, w zależności od pierwszego parametru funkcji.
DATENAME: zwracająca nazwę wybranego składnika daty.
GETDATE(): wyświetla datę i godzinę teraźniejszą.
DATEDIFF: oblicza, ile czasu minęło pomiędzy dwiema podanymi datami
SUBSTRING: zwraca część danego ciągu.
LEN: zwraca liczbę znaków
CASE: wprowadza logikę IF
```

```sql
DATEPART(year, '2000-12-15') -- zwróci wartość 2000
DATEPART(year, '2000-12-15') -- zwróci wartość 15
DATENAME(weekday, '2000-12-15') -- zwróci Friday
YEAR('2000-12-15')  -- zwróci wartość 2000
DATEDIFF(day, '2022-01-01', '2022-02-01') -- zwróci 31
GETDATE() -- zwróci 2023-02-26 14:36:13.850
SUBSTRING('Hello, world!', 1, 5) -- zwróci Hello
LEN('Hello, world!') -- zwróci 13
```

<div class="page"/>
Jeżeli któraś z funkcji jest dla Ciebie niejasna, postaraj się ją wyszukać w internecie, najlepiej w oficjalnej dokumentacji. To również spora część pracy inżynierów danych i umiejętność, którą trzeba rozwijać.

<div class="page"/>

## Zadania

---

1. Dla każdego zamówienia wyświetl `order_id` oraz rok, w którym zamówienie zostało złożone (`order_date`) i podpisz je jako _order_year_.

2. Dla każdego zamówienia wyświetl order_date oraz dzień tygodnia w którym zostało złożone (order_date). Wyświetl tylko aktywne zamówienia.

3. Wyświetl wszystkich pracowników urodzonych w 1990 roku. Napisz podane zapytania na 3 różne sposoby.

4. Wyświetl bieżącą datę i godzinę.

5. Wyświetl, ile lat minęło od daty 1 stycznia 1990 roku.

6. Dla każdego zamówienia wyświetl `order_id` oraz ile dni temu zostało zamówione.

7. Dla każdego pracownika wyświetl imię, nazwisko oraz inicjały.

8. Wypisz wszystkie zamówienia, które zostały złożone w styczniu lub w lutym.

9. Wypisz nazwę oraz liczbę liter w nazwie dla tych produktów, których nazwa ma 7 lub więcej znaków.

10. Wyświetl nazwy oraz długość tych nazw dla dwóch produktów, których nazwy są najdłuższe. Jeśli istnieje więcej produktów, które mają długość nazwy taką samą, jak tego produktu na drugim miejscu, również je uwzględnij.

11. Wyświetl wszystkie unikalne wartości długości połączonego imienia i nazwiska (razem z odstępem) klientów. Wyniki posortuj rosnąco według tych wartości.

12. Wyświetl produkty, które zostały już wysłane i dodaj kolumnę, która pokazuje ile dni zajęło pomiędzy złożeniem zamówienia, a wysłaniem produktów.
