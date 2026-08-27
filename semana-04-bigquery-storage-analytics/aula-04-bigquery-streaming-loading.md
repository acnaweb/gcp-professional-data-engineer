# Aula 4 — Loading, Streaming e External Data

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
- Criar external table.
- Provocar schema drift.

# Conceito
External table consulta dados no source; native table oferece armazenamento otimizado no BigQuery. Streaming atende baixa latência contínua.

## Arquitetura construída
```text
GCS ─external→ query
GCS ─load→ native BQ
stream → write API/integration → BQ
```

# Criar
```bash
export EB="${PROJECT_ID}-pde-ext"; gcloud storage buckets create "gs://${EB}" --location=US
printf 'id,amount\n1,10\n2,20\n' > ext.csv
gcloud storage cp ext.csv "gs://${EB}/ext.csv"
bq mk --dataset --location=US "${PROJECT_ID}:retail_ext" 2>/dev/null || true
bq mkdef --source_format=CSV --autodetect "gs://${EB}/ext.csv" > extdef.json
bq mk --external_table_definition=extdef.json "${PROJECT_ID}:retail_ext.orders"
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_ext.orders"
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_ext.orders`'
```

# Quebrar propositalmente
printf 'id,amount\n1,abc\n' > bad.csv; gcloud storage cp bad.csv "gs://${EB}/ext.csv"; bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_ext.orders`'

# Troubleshooting
**Sintoma:** parse/schema error. **Hipótese:** source drift. **Evidência:** arquivo e external schema. **Causa:** tipo mudou. **Correção:** source/schema/staging seguro.

# Corrigir
Restaure CSV válido e consulte.

# Serviços semelhantes e critérios de escolha
Load=native performance; external=no-load convenience; BigLake=governance lake; streaming=low-latency writes.

# Pegadinhas e decisões típicas da prova
External não significa zero custo nem mesma performance de native. Schema drift é risco operacional.

# Questões estilo Professional Data Engineer
1. Consultar GCS sem load: qual opção?
2. Dashboard intensivo recorrente: external ou native?
3. Low-latency continuous ingest: load batch?

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
bq rm -r -f "${PROJECT_ID}:retail_ext"; gcloud storage rm -r "gs://${EB}"; rm -f ext.csv bad.csv extdef.json
```
