# Aula 3 — Data Quality

## Dimensões
- Completeness
- Accuracy
- Validity
- Uniqueness
- Consistency
- Timeliness

## Modelo
```text
Ingestion
  ↓
Quality checks
  ↓
Quarantine / alert / continue
```

## Questões
Falha crítica de qualidade deve impedir promoção para camada trusted?  
**Sim, se regra de negócio exigir qualidade como gate.**

## Checklist
- [ ] Sei definir regras mensuráveis
