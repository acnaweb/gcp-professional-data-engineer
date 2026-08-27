# Aula 2 — Modelagem de Dados: OLTP, OLAP e Analytics

## Case contínuo — RetailPulse

A RetailPulse é um e-commerce que evoluirá durante o roadmap: arquivos batch, eventos em streaming, CDC, BigQuery, qualidade, governança, orquestração, observabilidade e serving.

Use preferencialmente um projeto de laboratório:

```bash
export PROJECT_ID="$(gcloud config get-value project)"
export REGION="us-central1"
export BQ_LOCATION="US"
export BUCKET="${PROJECT_ID}-pde-retailpulse"
```

> Alguns recursos podem gerar cobrança. Recursos caros ou dependentes de Organization/integração externa são tratados com laboratório de inspeção, decisão e troubleshooting quando provisioná-los apenas para estudo não é razoável.


## Objetivos
- Diferenciar OLTP e OLAP.
- Criar fato particionada e clusterizada.
- Forçar erro de partition filter.

# Conceito
OLTP privilegia transações e consistência operacional; OLAP privilegia scans, agregações e análise. BigQuery favorece modelos analíticos, com partitioning, clustering e, quando útil, nested/repeated fields.

## Arquitetura construída
```text
raw.orders → fact_orders(partition date, cluster customer/status) → analytics
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_dw" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_dw.fact_orders`
PARTITION BY DATE(order_ts)
CLUSTER BY customer_id,status
OPTIONS(require_partition_filter=true) AS
SELECT order_id,customer_id,TIMESTAMP(order_ts) order_ts,CAST(amount AS NUMERIC) amount,status
FROM `'"${PROJECT_ID}"'.retail_raw.orders`;'
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_dw.fact_orders"
bq query --use_legacy_sql=false '
SELECT partition_id,total_rows
FROM `'"${PROJECT_ID}"'.retail_dw.INFORMATION_SCHEMA.PARTITIONS`
WHERE table_name="fact_orders";'
```

# Testar
```bash
bq query --use_legacy_sql=false '
SELECT customer_id,SUM(amount) revenue
FROM `'"${PROJECT_ID}"'.retail_dw.fact_orders`
WHERE DATE(order_ts)="2026-08-26"
GROUP BY customer_id;'
```

# Quebrar propositalmente
```bash
bq query --use_legacy_sql=false '
SELECT status,COUNT(*) FROM `'"${PROJECT_ID}"'.retail_dw.fact_orders` GROUP BY status;'
```

# Troubleshooting
**Sintoma:** BigQuery exige filtro de partição.  
**Hipótese:** `require_partition_filter=true`.  
**Evidência:** `bq show`.  
**Causa:** query não restringe `order_ts`.  
**Correção:** adicionar predicado utilizável na coluna particionada.

# Corrigir
```bash
bq query --use_legacy_sql=false '
SELECT status,COUNT(*) FROM `'"${PROJECT_ID}"'.retail_dw.fact_orders`
WHERE DATE(order_ts) BETWEEN "2026-08-01" AND "2026-08-31"
GROUP BY status;'
```

# Serviços semelhantes e critérios de escolha
- Cloud SQL/AlloyDB/Spanner: operacional relacional.
- BigQuery: analítico.
- Partition: pruning macro.
- Clustering: organização para filtros/agregações recorrentes.

Partition + clustering podem coexistir.

# Pegadinhas e decisões típicas da prova
- `LIMIT` não garante menos bytes lidos.
- Particionar por alta cardinalidade arbitrária não equivale a índice.
- BigQuery não é store OLTP padrão para APIs transacionais.

# Questões estilo Professional Data Engineer
1. Tabela de 20 TB filtrada por data: primeira otimização?
2. Dentro do dia, filtros por customer_id: o que considerar?
3. API com updates transacionais de baixa latência: BigQuery é default?

> Responda justificando **por que** a alternativa escolhida atende melhor aos requisitos e qual trade-off elimina as demais.


# Checklist final
- [ ] Entendi o conceito e os trade-offs.
- [ ] Executei o laboratório aplicável.
- [ ] Usei comandos de inspeção/list/describe.
- [ ] Provoquei a falha proposta.
- [ ] Segui sintoma → hipótese → evidência → causa → correção.
- [ ] Sei escolher entre serviços semelhantes.
- [ ] Executei ou revisei o cleanup.

# Cleanup
```bash
bq rm -r -f "${PROJECT_ID}:retail_dw"
```
