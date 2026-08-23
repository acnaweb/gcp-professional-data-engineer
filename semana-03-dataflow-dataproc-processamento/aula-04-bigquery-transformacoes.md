# Aula 4 — Transformações no BigQuery

## Objetivos
- Usar SQL para ELT.
- Entender MERGE, CTAS, partition pruning e materialized views.

## Exemplo
```sql
MERGE target T
USING staging S
ON T.id = S.id
WHEN MATCHED THEN UPDATE SET amount = S.amount
WHEN NOT MATCHED THEN INSERT (id, amount) VALUES(id, amount);
```

## Boas práticas
- Filtrar partições.
- Evitar scans desnecessários.
- Usar scheduled queries quando simples.
- Usar Dataform para dependências/modelagem versionada.

## Checklist
- [ ] Sei quando ELT em BigQuery é suficiente
- [ ] Entendo MERGE
