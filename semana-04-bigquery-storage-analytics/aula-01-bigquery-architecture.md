# Aula 1 — BigQuery Architecture e Storage

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
- Criar tabela particionada/clusterizada.
- Comparar bytes com dry run.

# Conceito
BigQuery separa storage columnar de compute distribuído. Partition pruning e seleção de colunas reduzem leitura.

## Arquitetura construída
```text
columnar storage ↔ distributed slots → query result
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_bq" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_bq.events`
PARTITION BY event_date CLUSTER BY customer_id AS
SELECT DATE_ADD(DATE "2026-01-01",INTERVAL MOD(x,90) DAY) event_date,
CONCAT("c",MOD(x,1000)) customer_id,x id,RAND()*100 value
FROM UNNEST(GENERATE_ARRAY(1,100000)) x;'
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_bq.events"
```

# Testar
```bash
bq query --use_legacy_sql=false --dry_run 'SELECT SUM(value) FROM `'"${PROJECT_ID}"'.retail_bq.events`'
bq query --use_legacy_sql=false --dry_run 'SELECT SUM(value) FROM `'"${PROJECT_ID}"'.retail_bq.events` WHERE event_date="2026-02-01"'
```

# Quebrar propositalmente
Execute full scan sem filtro de data.

# Troubleshooting
**Sintoma:** bytes altos. **Hipótese:** sem pruning/colunas excessivas. **Evidência:** dry run/query plan. **Causa:** scan amplo. **Correção:** partition filter e colunas mínimas.

# Corrigir
Adicione filtro de partição e repita dry run.

# Serviços semelhantes e critérios de escolha
Partitions=pruning; clustering=filtros seletivos; slots=compute; materialized views=pré-computação compatível.

# Pegadinhas e decisões típicas da prova
Mais slots não corrige query que lê dados desnecessários. `LIMIT` não reduz necessariamente scan.

# Questões estilo Professional Data Engineer
1. 10 linhas retornadas, 5 TB lidos: LIMIT resolve?
2. Filtro diário: partition?
3. Filtro recorrente customer dentro da partição: clustering?

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
bq rm -r -f "${PROJECT_ID}:retail_bq"
```
