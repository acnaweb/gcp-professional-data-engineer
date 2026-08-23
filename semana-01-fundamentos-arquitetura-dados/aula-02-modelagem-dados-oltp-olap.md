# Aula 2 — Modelagem de Dados: OLTP, OLAP e Analytics

## Objetivos
- Diferenciar OLTP e OLAP.
- Revisar normalização, desnormalização e modelagem dimensional.
- Entender impacto da modelagem no BigQuery.

## OLTP x OLAP
| Aspecto | OLTP | OLAP |
|---|---|---|
| Operação | Transações | Análise |
| Modelo | Normalizado | Dimensional/desnormalizado |
| Latência | Baixa por operação | Otimizada para scans/agregações |
| Serviços | Cloud SQL, Spanner | BigQuery |

## Star Schema
```text
        dim_customer
             |
dim_date — fact_sales — dim_product
             |
         dim_store
```

## BigQuery
Boas práticas:
- Particionar por coluna temporal ou inteira adequada.
- Clusterizar por colunas muito filtradas/agregadas.
- Evitar `SELECT *` sem necessidade.
- Preferir nested/repeated quando fizer sentido.

## Laboratório
Crie tabela particionada:
```sql
CREATE TABLE pde_lab.sales (
  order_id STRING,
  customer_id STRING,
  order_date DATE,
  amount NUMERIC
)
PARTITION BY order_date
CLUSTER BY customer_id;
```

## Questões
- Workload analítico com muitos joins e scans? **BigQuery.**
- Transações globais relacionais fortes? **Spanner.**

## Checklist
- [ ] Diferencio OLTP e OLAP
- [ ] Sei explicar star schema
- [ ] Entendo partitioning e clustering
