# Aula 1 — Cloud Composer e Airflow

## Objetivos
- Entender DAGs, tasks, operators, retries e scheduling.
- Saber quando usar Composer.

## Modelo
```text
DAG
 ├── extract
 ├── transform
 └── load
```

Use quando:
- Há múltiplos serviços/pipelines.
- Dependências complexas.
- Scheduling/orquestração é requisito.

## Checklist
- [ ] Entendo DAG
- [ ] Entendo retries
