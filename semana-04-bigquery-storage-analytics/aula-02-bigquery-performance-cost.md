# Aula 2 — Performance e Custo no BigQuery

## Objetivos
- Reduzir bytes processados.
- Entender reservations e autoscaling slots em nível conceitual.

## Boas práticas
- Evitar SELECT *
- Filtrar partições
- Clusterizar
- Materialized views
- BI Engine quando aplicável
- Reservations para previsibilidade

## Questão
Workloads concorrentes com necessidade de capacidade previsível?  
**Considere reservations/editions apropriadas.**

## Checklist
- [ ] Sei reduzir scan
- [ ] Entendo reservations
