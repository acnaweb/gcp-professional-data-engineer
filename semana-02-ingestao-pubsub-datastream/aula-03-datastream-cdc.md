# Aula 3 — Datastream e CDC

## Objetivos
- Entender Change Data Capture.
- Posicionar Datastream em arquiteturas.

## Modelo
```text
Operational DB
    ↓ CDC
Datastream
    ↓
Cloud Storage / BigQuery / pipeline downstream
```

Use quando precisa capturar mudanças sem batch full reload.

## Pontos
- Log-based CDC
- Initial backfill
- Ongoing changes
- Schema evolution
- Monitoring

## Questão
Migrar mudanças de banco relacional continuamente sem impacto elevado na origem?  
**Datastream** é candidato forte.

## Checklist
- [ ] Entendo CDC
- [ ] Entendo backfill + incremental
