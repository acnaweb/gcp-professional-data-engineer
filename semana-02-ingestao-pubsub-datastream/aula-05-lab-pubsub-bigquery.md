# Aula 5 — Pub/Sub para Analytics

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
- Construir topic + tabela de destino.
- Validar producer e contrato.
- Quebrar payload JSON.

# Conceito
Integração direta Pub/Sub→BigQuery é útil quando não há transformação complexa. Dataflow entra quando há parsing, enriquecimento, windowing, DLQ ou lógica de negócio.

## Arquitetura construída
```text
publisher → Pub/Sub → (direct subscription or Dataflow) → BigQuery
```

# Criar
```bash
gcloud services enable pubsub.googleapis.com bigquery.googleapis.com
bq mk --dataset --location=US "${PROJECT_ID}:retail_stream" 2>/dev/null || true
gcloud pubsub topics create retail-bq-events
gcloud pubsub subscriptions create retail-debug --topic=retail-bq-events
gcloud pubsub topics publish retail-bq-events --message='{"event_id":"e1","page":"home"}'
```

# Inspecionar
```bash
gcloud pubsub topics describe retail-bq-events
gcloud pubsub subscriptions describe retail-debug
bq show "${PROJECT_ID}:retail_stream"
```

# Testar
```bash
gcloud pubsub subscriptions pull retail-debug --auto-ack --limit=1
```

# Quebrar propositalmente
```bash
gcloud pubsub topics publish retail-bq-events --message='NOT_JSON'
gcloud pubsub subscriptions pull retail-debug --auto-ack --limit=1
```

# Troubleshooting
**Sintoma:** Pub/Sub aceita bytes, mas sink que espera JSON/schema pode falhar. **Hipótese:** contrato de payload. **Evidência:** mensagem bruta vs schema esperado. **Causa:** producer enviou payload inválido. **Correção:** corrigir producer ou tratar inválidos em Dataflow/DLQ.

# Corrigir
```bash
gcloud pubsub topics publish retail-bq-events --message='{"event_id":"e2","page":"product"}'
```

# Serviços semelhantes e critérios de escolha
Direct subscription = simples; Dataflow = transformação/windowing; custom subscriber = lógica/controle específico.

# Pegadinhas e decisões típicas da prova
Pub/Sub não valida automaticamente seu contrato JSON de negócio. Mais componentes não significam arquitetura melhor.

# Questões estilo Professional Data Engineer
1. Sem transformação: direct BigQuery integration ou Dataflow?
2. Precisa enriquecer e separar inválidos: qual?
3. `NOT_JSON` chegar ao topic prova validação de negócio?

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
gcloud pubsub subscriptions delete retail-debug --quiet
gcloud pubsub topics delete retail-bq-events --quiet
bq rm -r -f "${PROJECT_ID}:retail_stream"
```
