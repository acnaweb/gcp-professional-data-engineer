# Aula 5 — Partitioning e Clustering

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
- Medir metadata e forçar require_partition_filter.

# Conceito
`require_partition_filter` protege contra scans acidentais. Clustering complementa partitioning para padrões seletivos.

## Arquitetura construída
```text
events(partition d, cluster customer_id)
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_pc" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_pc.events`
PARTITION BY d CLUSTER BY customer_id OPTIONS(require_partition_filter=true) AS
SELECT DATE_ADD(DATE "2026-01-01",INTERVAL MOD(x,60) DAY) d,CONCAT("c",MOD(x,500)) customer_id,x id
FROM UNNEST(GENERATE_ARRAY(1,100000)) x;'
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_pc.events"
```

# Testar
Query com `WHERE d="2026-02-01" AND customer_id="c10"`.

# Quebrar propositalmente
Query sem filtro de `d`.

# Troubleshooting
**Sintoma:** erro de partition filter. **Hipótese:** option habilitada. **Evidência:** metadata. **Causa:** filtro ausente. **Correção:** predicado na partição.

# Corrigir
Adicione faixa de `d` e reexecute.

# Serviços semelhantes e critérios de escolha
Partition para pruning; clustering para organização seletiva. Não trate clustering como índice B-tree.

# Pegadinhas e decisões típicas da prova
Ordem de colunas de clustering importa. Alta cardinalidade é aceitável para clustering, mas não implica particionar por ela.

# Questões estilo Professional Data Engineer
1. Evitar scans sem data: qual option?
2. Customer filter dentro do dia: qual design?
3. Partition por milhões de customer IDs é boa regra?

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
bq rm -r -f "${PROJECT_ID}:retail_pc"
```
