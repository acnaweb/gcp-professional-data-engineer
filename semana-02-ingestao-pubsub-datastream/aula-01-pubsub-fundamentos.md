# Aula 1 — Pub/Sub: Fundamentos

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
- Criar topic/subscription.
- Validar fan-out, ACK e redelivery.

# Conceito
Pub/Sub desacopla produtores e consumidores. Topic recebe mensagens; cada subscription representa um fluxo de consumo independente.

## Arquitetura construída
```text
publisher → topic → subscription(s) → consumers
```

# Criar
```bash
gcloud services enable pubsub.googleapis.com
gcloud pubsub topics create retail-events
gcloud pubsub subscriptions create retail-events-sub --topic=retail-events --ack-deadline=10
for i in 1 2 3; do gcloud pubsub topics publish retail-events --message="{\"id\":\"e${i}\"}"; done
```

# Inspecionar
```bash
gcloud pubsub topics list
gcloud pubsub topics describe retail-events
gcloud pubsub subscriptions describe retail-events-sub
```

# Testar
```bash
gcloud pubsub subscriptions pull retail-events-sub --limit=2 --auto-ack
```

# Quebrar propositalmente
```bash
gcloud pubsub topics publish retail-events --message='{"id":"redelivery"}'
gcloud pubsub subscriptions pull retail-events-sub --limit=1
sleep 12
gcloud pubsub subscriptions pull retail-events-sub --limit=5
```

# Troubleshooting
**Sintoma:** redelivery. **Hipótese:** ACK ausente. **Evidência:** deadline e primeiro pull. **Causa:** mensagem não reconhecida. **Correção:** ACK pós-processamento + idempotência.

# Corrigir
```bash
gcloud pubsub subscriptions pull retail-events-sub --limit=10 --auto-ack
```

# Serviços semelhantes e critérios de escolha
Fan-out = várias subscriptions. CDC de banco = Datastream. Orquestração = Composer/Workflows. Pub/Sub transporta eventos; não substitui engine de transformação.

# Pegadinhas e decisões típicas da prova
Ordering não é ordem global automática. Retry não substitui DLQ. Duas subscriptions recebem cópias lógicas independentes.

# Questões estilo Professional Data Engineer
1. Dois consumidores precisam de todos os eventos: como modelar?
2. Duplicatas possíveis: qual defesa?
3. CDC do WAL deve usar Pub/Sub diretamente?

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
gcloud pubsub subscriptions delete retail-events-sub --quiet
gcloud pubsub topics delete retail-events --quiet
```
