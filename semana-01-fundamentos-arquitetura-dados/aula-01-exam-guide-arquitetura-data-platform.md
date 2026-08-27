# Aula 1 — Exam Guide e Arquitetura de Data Platform

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
- Traduzir requisitos de negócio em uma arquitetura de dados.
- Criar a base do case com Cloud Storage e BigQuery.
- Diagnosticar API desabilitada.

# Conceito
A PDE cobra design, ingestão/processamento, storage, preparação/uso para análise e manutenção/automação. O raciocínio começa por volume, latência, SLA, segurança, consistência, custo e operação — não pelo nome do produto.

## Arquitetura construída
```text
CSV → Cloud Storage → BigQuery RAW → SQL de validação
```

# Criar
```bash
gcloud services enable storage.googleapis.com bigquery.googleapis.com
gcloud storage buckets create "gs://${BUCKET}" --location="${REGION}" --uniform-bucket-level-access
bq --location="${BQ_LOCATION}" mk --dataset "${PROJECT_ID}:retail_raw"
cat > orders.csv <<'EOF'
order_id,customer_id,order_ts,amount,status
o1,c1,2026-08-26T10:00:00Z,150.00,PAID
o2,c2,2026-08-26T10:05:00Z,79.90,PAID
EOF
gcloud storage cp orders.csv "gs://${BUCKET}/landing/orders.csv"
bq load --autodetect --source_format=CSV --skip_leading_rows=1  "${PROJECT_ID}:retail_raw.orders" "gs://${BUCKET}/landing/orders.csv"
```

# Inspecionar
```bash
gcloud storage buckets describe "gs://${BUCKET}"
gcloud storage ls -r "gs://${BUCKET}/**"
bq show --schema --format=prettyjson "${PROJECT_ID}:retail_raw.orders"
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT status,COUNT(*) qtd,SUM(amount) total FROM `'"${PROJECT_ID}"'.retail_raw.orders` GROUP BY status'
```

# Quebrar propositalmente
```bash
gcloud services disable bigquery.googleapis.com --force
bq ls
```

# Troubleshooting
**Sintoma:** `bq` informa API desabilitada.  
**Hipótese:** API ou projeto incorreto.  
**Evidência:** `gcloud services list --enabled --filter="config.name:bigquery.googleapis.com"` e `gcloud config get-value project`.  
**Causa:** BigQuery API foi desabilitada.  
**Correção:** reabilitar a API. Não tente resolver com uma role mais ampla.

# Corrigir
```bash
gcloud services enable bigquery.googleapis.com
bq ls
```

# Serviços semelhantes e critérios de escolha
- Cloud Storage: object/data lake/landing.
- BigQuery: warehouse analítico SQL.
- Pub/Sub: eventos desacoplados.
- Dataflow: Beam, batch/stream e janelas.
- Dataproc: Spark/Hadoop.
- Datastream: CDC.

Escolha pelo padrão e NFRs, não por familiaridade.

# Pegadinhas e decisões típicas da prova
- `403`/falha pode ser API, IAM, perímetro ou quota.
- Serviço gerenciado é forte candidato quando atende todos os requisitos.
- Não adicione Dataflow apenas porque “é Data Engineering”.

# Questões estilo Professional Data Engineer
1. CSV diário, analytics SQL e baixa operação: qual desenho inicial?
2. Spark legado com pouca reescrita: qual serviço?
3. API desabilitada com IAM correto: qual primeira ação?

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
Mantenha os recursos para a semana ou remova:
```bash
bq rm -r -f "${PROJECT_ID}:retail_raw"
gcloud storage rm -r "gs://${BUCKET}"
rm -f orders.csv
```
