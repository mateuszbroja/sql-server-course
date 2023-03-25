# Zadania dodatkowe

Zadanie bardziej złożone względem standardowych.

1. Stwórz dwie podane tabele i wrzuć tam dane. Mając dane o produktach znajdujących się w magazynie z tabeli **`warehouse_inventory_by_day`**, wyświetl ile jest produktów w każdym z magazynów dla każdego dnia znajdującego się w kalendarzu. Jeżeli nie ma produktów w danym dniu to pokaż **`0`**. Wyświetl:
- identyfikator magazynu,
- identyfikator produktu,
- data,
- ilość produktów.

```sql
CREATE TABLE warehouse_inventory_by_day (warehouse_id INT, product_type_id INT, item_id VARCHAR(32), inventory_date DATE);
CREATE TABLE calendar (calendar_date date);
```

```sql
INSERT INTO warehouse_inventory_by_day VALUES (1, 1, 'i11', '2020-08-04');
INSERT INTO warehouse_inventory_by_day VALUES (1, 1, 'i11', '2020-08-01');
INSERT INTO warehouse_inventory_by_day VALUES (1, 1, 'i12', '2020-08-03');
INSERT INTO warehouse_inventory_by_day VALUES (2, 2, 'i22', '2020-08-04');
INSERT INTO warehouse_inventory_by_day VALUES (1, 1, 'i11', '2020-08-04');
INSERT INTO warehouse_inventory_by_day VALUES (1, 3, 'i11', '2020-08-01');
INSERT INTO warehouse_inventory_by_day VALUES (1, 1, 'i12', '2020-08-03');
INSERT INTO warehouse_inventory_by_day VALUES (2, 3, 'i22', '2020-08-04');
INSERT INTO warehouse_inventory_by_day VALUES (2, 2, 'i22', '2020-08-04');
INSERT INTO warehouse_inventory_by_day VALUES (2, 1, 'i22', '2020-08-04');

INSERT INTO calendar VALUES ('2020-08-01');
INSERT INTO calendar VALUES ('2020-08-02');
INSERT INTO calendar VALUES ('2020-08-03');
INSERT INTO calendar VALUES ('2020-08-04');
```


Uzyskaj wynik podany poniżej:

| warehouse_id | product_type_id | calendar_date | quantity |
|--------------|-----------------|---------------|----------|
| 1            | 1               | 2020-08-01    | 1        |
| 1            | 1               | 2020-08-02    | 0        |
| 1            | 1               | 2020-08-03    | 2        |
| 1            | 1               | 2020-08-04    | 2        |
| 1            | 3               | 2020-08-01    | 1        |
| 1            | 3               | 2020-08-02    | 0        |
| 1            | 3               | 2020-08-03    | 0        |
| 1            | 3               | 2020-08-04    | 0        |
| 2            | 1               | 2020-08-01    | 0        |
| 2            | 1               | 2020-08-02    | 0        |
| 2            | 1               | 2020-08-03    | 0        |
| 2            | 1               | 2020-08-04    | 1        |
| 2            | 2               | 2020-08-01    | 0        |
| 2            | 2               | 2020-08-02    | 0        |
| 2            | 2               | 2020-08-03    | 0        |
| 2            | 2               | 2020-08-04    | 2        |
| 2            | 3               | 2020-08-01    | 0        |
| 2            | 3               | 2020-08-02    | 0        |
| 2            | 3               | 2020-08-03    | 0        |
| 2            | 3               | 2020-08-04    | 1        |
