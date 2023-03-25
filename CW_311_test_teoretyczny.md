# 311. Test teoretyczny

Pytania tutaj zawarte bardzo często zdarzają się na rozmowa kwalifikacyjnych. Wykonaj test bez patrzenia do materiałów pomocniczych. Nie przejmuj się pytaniami na, które nie znasz odpowiedzi; test zawiera przekrój pytań od podstawowych do bardzo zaawansowanych.

1. Jakie są rodzaje **`JOINów`**?
   
2. Jaka jest różnica pomiędzy **`UNION`**, a **`UNION ALL`**?
   
3. Czym są **`correlated sub-query`** i dlaczego ich wydajność jest gorsza od standardowych podzapytań.

4. Jak jest różnica pomiędzy **`DELETE`**, **`TRUNCATE`** and **`DROP`**?

5. Wymień kolejność wykonywanych poleceń przez silnik SQL (wymień 10).

6. Po co używać indeksów?

7. Jakie jest domyślna kolejność sortowania przez **`ORDER BY`**?

8. Co to jest normalizacja?

9. Jaka jest różnica pomiędzy **`UNION`**, **`EXCEPT`** a **`INTERSECT`**?

10. Co to jest klucz główny oraz klucz obcy?

11. Czym różni się **`WHERE`** od **`HAVING`**?

12. Co dodamy do zapytania SELECT, żeby uzyskać wynik bez duplikatów?

13. Czy dobrym pomysłem jest składowanie finansowych danych w typie **`FLOAT`**? Dlaczego?

14. Ile **`clustered index`** może mieć tabela?

15. Jaka jest największa wada **`clustered index`**?

16. Jaki typ danych jest najlepszy do składowania numeru telefonu?

17. Po co używać funkcji okienkowych?

18. Co to jest **`CTE`** i po co je używać?

19. Jaka jest różnica pomiędzy **`LEAD`** oraz `**LAG**`?

20. Czym różni się widok od widoku zmaterializowanego?

21. Czym różni się widok od tabeli?

22. Jak sprawdzić wydajność naszego zapytania?

23. Jakie są różne rodzaje operacji SQL (nie zapytania)?

24. Jakie są rodzaje relacji pomiędzy tabelami?

25. Jeżeli łączymy tabelę A z tabelą B przy użyciu **`INNER JOIN`**, to w jakiej sytuacji w wyniku będzie więcej rekordów niż w tabeli A?

26. Jaka jest różnica pomiędzy **`RANK()`**, **`DENSE_RANK()`**, a **`ROW_NUMBER()`**?

27. Jak stworzyć tabelę tymczasową?

28. Kiedy użyjemy typ **`VARCHAR`**, a kiedy **`NVARCHAR`**?

29. Podaj 3 różnice pomiędzy **`clustered`**, a **`non-clustered index`**.

30. Co robi funkcja **`COALESCE`**?

31. Czym jest procedura?

32. Jak działa **`CROSS JOIN`**?

33. Czym jest alias?

34. Czym jest denormalizacja i kiedy może być bardziej pożądana niż denormalizacja?

35. Jakie są funkcje agregujące?

36. Jakie znasz **`wildcards`** w syntaksie SQL (wymień dwa)?

37. Jakie komendy użyjesz do, która obsłużyłaby **`INSERT`**, **`UPDATE`** oraz **`DELETE`**?

38. Czy jest możliwe sortowanie poprzez alias?

39. Czy pusta komórka to to samo co **`NULL`**?

40. Wymień błędy, które widzisz w podanym zapytaniu:

```sql
SELECT order_id, AVG(quantity) as avg_quantity
FROM orders
WHERE avg_quantity > 200
GROUP BY order_id
```

41. Czy możemy używać **`HAVING`** przy operacjach **`UPDATE`**?

42. Co to jest plan wykonania zapytania (**`query execution plan`**) i jakie informacje można z niego odczytać?

43. Co to jest funkcja **`CAST`** i jakie są jej zastosowania w SQL?