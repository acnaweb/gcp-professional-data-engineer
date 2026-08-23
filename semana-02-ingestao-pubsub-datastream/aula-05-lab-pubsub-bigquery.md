# Aula 5 — Lab: Pub/Sub para Analytics

## Arquitetura
```text
Producer → Pub/Sub → Dataflow / subscription integration → BigQuery
```

## Lab mínimo
Crie topic e subscription e publique eventos JSON.

```bash
gcloud pubsub topics create orders
gcloud pubsub subscriptions create orders-sub --topic=orders
```

Publique:
```bash
for i in 1 2 3; do
  gcloud pubsub topics publish orders --message="{\"order_id\":$i,\"amount\":100}"
done
```

## Extensão
Implemente pipeline Dataflow na semana 3.

## Checklist
- [ ] Topic criado
- [ ] Eventos publicados
- [ ] Subscription validada
