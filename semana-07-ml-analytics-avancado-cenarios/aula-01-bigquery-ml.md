# Aula 1 — BigQuery ML

## Objetivos
- Entender quando treinar modelos diretamente em SQL.

## Exemplo
```sql
CREATE MODEL `pde_lab.model`
OPTIONS(model_type='logistic_reg') AS
SELECT label, feature1, feature2
FROM `pde_lab.training`;
```

## Use quando
- Dados já estão no BigQuery.
- Modelos suportados atendem.
- Equipe SQL-first.

## Checklist
- [ ] Entendo BigQuery ML
