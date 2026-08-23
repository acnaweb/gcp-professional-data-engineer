# Aula 1 — Pub/Sub: Fundamentos

## Objetivos
- Entender topics, subscriptions, ack e retention.
- Diferenciar push, pull e exactly-once em nível conceitual.

## Modelo
```text
Publisher → Topic → Subscription → Subscriber
```

## Conceitos
- At-least-once por padrão em muitos cenários.
- Ack deadline.
- Retention.
- Ordering keys.
- Dead-letter topics.
- Exactly-once delivery quando compatível.

## Lab
```bash
gcloud pubsub topics create pde-events
gcloud pubsub subscriptions create pde-events-sub --topic=pde-events
gcloud pubsub topics publish pde-events --message='{"id":1}'
gcloud pubsub subscriptions pull pde-events-sub --auto-ack
```

## Checklist
- [ ] Entendo topic/subscription
- [ ] Entendo ack
- [ ] Entendo DLQ
