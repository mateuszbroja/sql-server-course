# 307. Podzapytania - ćwiczenia

Podzapytania pozwalają na korzystanie w zapytaniu z wyników innego zapytania. Możemy je zawrzeć w:

```yaml
SELECT: podzapytania, jako jedna z kolumn.

IN / NOT IN: sprawdź, czy wartości znajdują/nie znajdują się w zbiorze z podzapytania.

ANY / ALL: sprawdź, czy rekord jest równy/mniejszy/większy niż jakiekolwiek/wszystkie rekordy z zapytania. W porównaniu do poprzedniego, używa się tutaj =, <, >.

EXISTS / NOT EXISTS: sprawdź, czy wartości znajdują/nie znajdują się w zbiorze z podzapytania.

UPDATE / DELETE / INSERT: przeprowadź operację tylko na wybrany zbiorze.

FROM: przeprowadź kolejną operację SELECT na wyniku innego zapytania.
```

Przykład:

```sql
SELECT
    order_id,
    order_date,
    customer_id
FROM orders
WHERE customer_id IN # tutaj zaczyna się subquery
(
	SELECT customer_id
	FROM customers
	WHERE first_name = 'Ewa'
)
ORDER BY order_date DESC;
```

Przykład z SELECT:

```sql
SELECT
	order_item_id,
	order_id,
	quantity,
	(
	    SELECT MAX(price)
	    FROM products p
	    WHERE i.product_id = o.product_id
	) AS maxprice
FROM order_items o
order by quantity;
```

Powyższy przykład to **`correlated sub query`**, czyli takie podzapytanie, które używa zapytania zewnętrznego (stąd użycie klucza). Każdy rekord jest wtedy kalkulowany osobno i przekazywany do podzapytania, tym samym obniża znacząco wydajność.

Podczas zadań możesz stosować różne sposoby na uzyskanie wyniku, ale trzymaj się użycia podzapytań. Jeżeli użycie któregoś z syntax jest dla Ciebie niezrozumiałe, sprawdź je w Google.

<div class="page"/>

## Zadania

W zadaniach nie używaj **`JOIN`**, chyba że polecenie mówi inaczej.

1. Wyświetl produkty, które kosztują więcej niż średnia wszystkich produktów.

![Picture](images/images_results/30701.png)

---

2. Zwróć nazwę produktu oraz jego cenę. Lista powinna zawierać tylko produkty, które posiadają cenę większą niż jakakolwiek średnia cena produktów w danym brandzie. Posegreguj wyniki po cenie, od największej do najmniejszej.

![Picture](images/images_results/30702.png)

---

3. Zwróć nazwę produktu oraz jego cenę. Lista powinna zawierać tylko produkty, które posiadają cenę większą niż wszystkie średnie ceny produktów w danym brandzie. Posegreguj wyniki po cenie, od największej do najmniejszej.

![Picture](images/images_results/30703.png)

---

4. Znajdź klientów, którzy nie kupili nic w **`2013`** roku. Przedstaw dwa sposoby na utrzymanie wyniku (użyj **`IN`** oraz **`EXISTS`**).

![Picture](images/images_results/30704.png)

---

5. Wyświetl **`customer_id`** klientów, którzy mieszkają w tym samym mieście, co jest sklep. Masz do dyspozycji tylko jeden **`JOIN`**.

![Picture](images/images_results/30705.png)

---

6. Zwróć średnią ilość zamówień jaką przyjmuje pracownik.

![Picture](images/images_results/30706.png)

---

7. Wyświetl produkty, które były zamówione więcej niż 10 razy w jednym zamówieniu.

![Picture](images/images_results/30707.png)

---

8. Wyświetl dane wszystkich pracowników, którzy zarabiają o minimum 30% mniej niż jest średnia wszystkich pensji w firmie.

![Picture](images/images_results/30708.png)

---

9. Wyświetl liczbę wszystkich pracowników oraz liczbę pracowników, którzy już nie pracują. Nie używaj **`UNION`**.

![Picture](images/images_results/30709.png)

---

10. Wyświetl pracowników, którzy mają nazwiska w **`TOP 3`** najdłuższych nazwisk.

![Picture](images/images_results/30710.png)

---

11. Wyświetl pracowników, ich stawkę oraz pozycję, którzy zarabiają mniej niż wynosi średnia zarobków na ich pozycji.

![Picture](images/images_results/30711.png)

---

12. Wyświetl dane pracowników, którzy pracują najdłużej.

![Picture](images/images_results/30712.png)

---

13. Wyświetl klientów oraz ich procentowy udział zamówień do liczby wszystkich zamówień.

![Picture](images/images_results/30713.png)

---

14. Dla każdego produktu wyświetl różnicę jego ceny do średniej ceny produktów w tej samej kategorii.

![Picture](images/images_results/30714.png)

---

15. Wyświetl jaka jest maksymalna liczba wszystkich zamówionych rzeczy z jednego zamówienia.

![Picture](images/images_results/30715.png)

---

16. Dla każdej kategorii wyświetl kiedy po raz pierwszy został zamówiony produkt z tej kategorii. Możesz użyć trzy **`JOIN`**.

![Picture](images/images_results/30716.png)

---

17. Wyświetl pracowników, których pensja jest pomiędzy najmniejszą pensją w firmie, a **28 000 $**.

![Picture](images/images_results/30717.png)

---

18. Wyświetl pracowników, którzy dostają drugą największą wypłatę w firmie.

![Picture](images/images_results/30718.png)

---

19. Zwróć wszystkie produkty, które są z tej samej firmy, co produkt **`GeForce RTX 3060`**.

![Picture](images/images_results/30719.png)

---

20. Znajdź pracowników, którzy zarabiają więcej niż najmniej zarabiający menadżer.

![Picture](images/images_results/30720.png)

---

21. Wyświetl pracowników, których pensja jest wyższa niż średnia pensja w z każdej z pozycji.

![Picture](images/images_results/30721.png)

---

22. Dla każdego produktu wyświetl kategorię oraz liczbę produktów z tą samą kategorią.

![Picture](images/images_results/30722.png)
