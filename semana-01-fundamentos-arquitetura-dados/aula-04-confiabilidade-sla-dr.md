# Aula 4 — Confiabilidade, SLA, DR e Resiliência

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
- Entender RPO/RTO, ACK, retry, DLQ e idempotência.
- Provocar redelivery no Pub/Sub.

# Conceito
RPO define perda de dados tolerável; RTO define tempo de recuperação. Pipelines confiáveis tratam retries, duplicatas, poison data, checkpoint/reprocessamento e observabilidade.

## Arquitetura construída
```text
publisher → topic → subscription → consumer
                         ├─ ACK
                         └─ redelivery/DLQ
```

# Criar
```bash
gcloud services enable pubsub.googleapis.com
gcloud pubsub topics create retail-reliability
gcloud pubsub subscriptions create retail-reliability-sub  --topic=retail-reliability --ack-deadline=10 --message-retention-duration=1d
gcloud pubsub topics publish retail-reliability --message='{"id":"e1"}'
```

# Inspecionar
```bash
gcloud pubsub topics describe retail-reliability
gcloud pubsub subscriptions describe retail-reliability-sub
```

# Testar
```bash
gcloud pubsub subscriptions pull retail-reliability-sub --auto-ack --limit=1
```

# Quebrar propositalmente
```bash
gcloud pubsub topics publish retail-reliability --message='{"id":"redeliver"}'
gcloud pubsub subscriptions pull retail-reliability-sub --limit=1
sleep 12
gcloud pubsub subscriptions pull retail-reliability-sub --limit=5
```

# Troubleshooting
**Sintoma:** mensagem reaparece.  
**Hipótese:** não houve ACK.  
**Evidência:** pull sem auto-ack + ack deadline.  
**Causa:** mensagem permaneceu pendente.  
**Correção:** ACK só após processamento bem-sucedido e consumidor idempotente.

# Corrigir
```bash
gcloud pubsub subscriptions pull retail-reliability-sub --auto-ack --limit=10
```

# Serviços semelhantes e critérios de escolha
- Retry/backoff: falha transitória.
- DLQ/quarantine: falha persistente/poison message.
- Idempotência: evita efeitos duplicados.
- Exactly-once é específico e não elimina todos os problemas de idempotência.

# Pegadinhas e decisões típicas da prova
- ACK cedo demais pode causar perda lógica.
- ACK tarde demais pode causar redelivery.
- Retry infinito de dado inválido é mau design.

# Questões estilo Professional Data Engineer
1. Aceita perder 5 min de dados: RPO ou RTO?
2. Mensagem inválida falha sempre: retry infinito ou DLQ?
3. Entrega at-least-once exige qual propriedade no consumer?

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
gcloud pubsub subscriptions delete retail-reliability-sub --quiet
gcloud pubsub topics delete retail-reliability --quiet
```
