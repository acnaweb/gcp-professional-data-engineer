# Aula 3 — Serving, Reverse ETL e Data Products

## Objetivos
- Entender que BigQuery nem sempre é serving store para APIs.
- Escolher storage operacional quando necessário.

## Modelo
```text
BigQuery
  ↓
Curated data
  ↓
Spanner / Cloud SQL / Bigtable / cache
  ↓
API
```

## Regra
Analytics store e operational serving store têm perfis diferentes.

## Checklist
- [ ] Sei separar analytics de serving
