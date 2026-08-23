# Aula 4 — Confiabilidade, SLA, DR e Resiliência

## Objetivos
- Projetar pipelines resilientes.
- Entender retries, idempotência, checkpoints e DLQ.
- Revisar RPO/RTO.

## Conceitos
```text
RPO → quanto dado posso perder
RTO → quanto tempo posso ficar indisponível
```

## Streaming resiliente
```text
Producer → Pub/Sub → Processor
                   ↘ DLQ
```

Boas práticas:
- Idempotência
- Retries com backoff
- Dead-letter topics
- Checkpointing
- Deduplicação
- Monitoring

## Batch resiliente
- Partition processing
- Reprocessamento seguro
- Tabelas staging
- MERGE quando apropriado
- Validação de qualidade

## Checklist
- [ ] Entendo RPO/RTO
- [ ] Entendo idempotência
- [ ] Sei quando usar DLQ
