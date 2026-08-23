# Aula 1 — Dataflow e Apache Beam

## Objetivos
- Entender Dataflow como serviço gerenciado para Beam.
- Diferenciar batch e streaming no mesmo modelo.

## Modelo
```text
Sources → PCollection → Transforms → Sinks
```

Beam:
- ParDo
- GroupByKey
- Combine
- Window
- Trigger

Dataflow:
- Autoscaling
- Managed workers
- Streaming Engine quando aplicável

## Regra de prova
Pipeline complexo batch/streaming, transformação em escala, windowing: **Dataflow**.

## Checklist
- [ ] Entendo Beam
- [ ] Entendo PCollection
- [ ] Entendo Dataflow batch/stream
