# Aula 3 — Serving e Reverse ETL

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
- Exportar curated data.
- Mostrar staleness entre source e destino.

# Conceito
Analytics store e serving store têm perfis distintos. Reverse ETL ativa dados analíticos em sistemas operacionais.

## Arquitetura construída
```text
BigQuery → export/sync → serving consumer
```

# Criar
```bash
export RB="${PROJECT_ID}-pde-serving"; gcloud storage buckets create "gs://${RB}" --location=US
bq mk --dataset --location=US "${PROJECT_ID}:retail_serving" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_serving.score` AS
SELECT "c1" id,0.9 score,CURRENT_TIMESTAMP() updated_at;'
bq extract --destination_format=NEWLINE_DELIMITED_JSON "${PROJECT_ID}:retail_serving.score" "gs://${RB}/v1/*.json"
```

# Inspecionar
```bash
gcloud storage cat "gs://${RB}/v1/"*.json
```

# Testar
Atualize score no BQ para 0.1 e compare com export v1.

# Quebrar propositalmente
Observe que export continua 0.9.

# Troubleshooting
**Sintoma:** serving stale. **Hipótese:** sync não rodou. **Evidência:** BQ updated_at x arquivo. **Causa:** batch snapshot. **Correção:** reexportar ou usar sync incremental/event-driven de acordo com SLA.

# Corrigir
Exporte para `v2`.

# Serviços semelhantes e critérios de escolha
BigQuery=analytics; Spanner/Cloud SQL/Bigtable/cache=serving conforme padrão; GCS=batch distribution.

# Pegadinhas e decisões típicas da prova
Dados no BQ não significam que API deve consultá-lo a cada request. Reverse ETL adiciona sincronização/duplicação.

# Questões estilo Professional Data Engineer
1. Lookup em ms e alto QPS: BQ por request?
2. Marketing aceita diário: batch export serve?
3. Score antigo: qual dimensão?

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
bq rm -r -f "${PROJECT_ID}:retail_serving"; gcloud storage rm -r "gs://${RB}"
```
