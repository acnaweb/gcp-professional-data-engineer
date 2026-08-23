# Aula 5 — Lab: Dataform

## Estrutura sugerida
```text
definitions/
  staging_orders.sqlx
  fact_orders.sqlx
  assertions.sqlx
```

Exemplo SQLX:
```sql
config {
  type: "table"
}

SELECT *
FROM ${ref("raw_orders")}
WHERE order_id IS NOT NULL
```

## Checklist
- [ ] Projeto criado
- [ ] Dependência criada
- [ ] Assertion criada
