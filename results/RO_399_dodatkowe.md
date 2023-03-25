# Zadania dodatkowe - rozwiązania

1. Zadanie z magazynem.

```sql
WITH agg_warehouse
AS (
  SELECT
    product_type_id,
    warehouse_id,
    inventory_date as table_dates,
    count(product_type_id) as item_count
  FROM warehouse_inventory_by_day
  GROUP BY
    product_type_id,
    warehouse_id,
    inventory_date)
SELECT
  j.warehouse_id,
  j.product_type_id,
  j.calendar_date,
  COALESCE(agg.item_count, 0) as quantity
FROM (
  SELECT *
  FROM calendar c
  CROSS JOIN (SELECT DISTINCT product_type_id, warehouse_id FROM agg_warehouse)
AS k) AS j
LEFT JOIN agg_warehouse agg ON j.calendar_date=agg.table_dates
                           AND j.product_type_id=agg.product_type_id
                           AND j.warehouse_id=agg.warehouse_id
```