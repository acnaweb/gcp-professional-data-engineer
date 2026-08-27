# Aula 2 — Integração com Vertex AI

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
- Preparar features.
- Inspecionar Vertex AI.
- Simular stale feature.

# Conceito
Data Engineer garante disponibilidade, qualidade, lineage e freshness das features; Vertex AI cobre ciclo de ML mais amplo.

## Arquitetura construída
```text
BQ curated → features → Vertex AI → predictions
```

# Criar
```bash
gcloud services enable aiplatform.googleapis.com
bq mk --dataset --location=US "${PROJECT_ID}:retail_features" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_features.f` AS
SELECT "c1" id,10 visits,CURRENT_TIMESTAMP() feature_ts UNION ALL SELECT "c2",2,CURRENT_TIMESTAMP();'
gcloud ai models list --region="${REGION}" 2>/dev/null || true
```

# Inspecionar
```bash
bq show --schema "${PROJECT_ID}:retail_features.f"
```

# Testar
Meça freshness com `TIMESTAMP_DIFF(CURRENT_TIMESTAMP(),MAX(feature_ts),MINUTE)`.

# Quebrar propositalmente
Atualize c2 para `TIMESTAMP_SUB(CURRENT_TIMESTAMP(),INTERVAL 2 DAY)`.

# Troubleshooting
**Sintoma:** prediction usa dado velho. **Hipótese:** feature pipeline atrasado. **Evidência:** feature_ts. **Causa:** freshness SLO violado. **Correção:** refresh/alert/gate.

# Corrigir
Atualize feature_ts para CURRENT_TIMESTAMP().

# Serviços semelhantes e critérios de escolha
BQ prepara batch features; Vertex AI treina/serve. Store online depende de requisito de latência.

# Pegadinhas e decisões típicas da prova
Training-serving skew e stale features são problemas de dados, não só ML.

# Questões estilo Professional Data Engineer
1. Feature <5 min: o que monitorar?
2. Custom training/endpoint: qual serviço?
3. SQL batch features: onde produzir?

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
bq rm -r -f "${PROJECT_ID}:retail_features"
```
