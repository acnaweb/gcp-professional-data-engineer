# Aula 5 — Lab: Partitioning e Clustering

## SQL
```sql
CREATE TABLE `pde_lab.events`
PARTITION BY DATE(event_ts)
CLUSTER BY customer_id AS
SELECT
  TIMESTAMP('2026-01-01') AS event_ts,
  'c1' AS customer_id,
  10 AS value;
```

Teste:
```sql
SELECT customer_id, SUM(value)
FROM `pde_lab.events`
WHERE DATE(event_ts) = '2026-01-01'
GROUP BY customer_id;
```

## Checklist
- [ ] Tabela particionada criada
- [ ] Filtro de partição usado
