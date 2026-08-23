# Aula 4 — Cenários Integrados

## Cenário 1
CDC PostgreSQL → analytics quase real time  
**Datastream + downstream storage/processamento adequado + BigQuery.**

## Cenário 2
Eventos milhões/s com janela temporal  
**Pub/Sub + Dataflow + BigQuery/Bigtable conforme serving.**

## Cenário 3
Spark legado com pouca reescrita  
**Dataproc.**

## Cenário 4
SQL ELT simples e versionado  
**BigQuery + Dataform.**

## Checklist
- [ ] Consigo justificar escolhas
