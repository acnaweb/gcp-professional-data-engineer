# Google Cloud Professional Data Engineer — Plano de Preparação

Repositório de preparação para a certificação **Google Cloud Professional Data Engineer (PDE)**.

## Formato atual do exame

- Duração: 2 horas
- Questões: 40–50
- Formato: múltipla escolha e múltipla seleção
- Idiomas: inglês e japonês
- Taxa: US$ 200 + impostos aplicáveis
- Pré-requisitos: nenhum
- Experiência recomendada: 3+ anos no setor, incluindo 1+ ano projetando e gerenciando soluções no Google Cloud

## Domínios

1. Design data processing systems
2. Ingest and process data
3. Store data
4. Prepare and use data for analysis
5. Maintain and automate data workloads

## Roadmap de 8 semanas

| Semana | Tema |
|---|---|
| 1 | Fundamentos, arquitetura, modelagem, segurança e resiliência |
| 2 | Ingestão: Pub/Sub, streaming, Datastream e transferências |
| 3 | Processamento: Dataflow, Beam, Dataproc e ELT |
| 4 | BigQuery, storage, performance, sharing e ingestão |
| 5 | Governança: Dataplex, Dataform, data quality e lineage |
| 6 | Orquestração, operação, monitoring, segurança e automação |
| 7 | BigQuery ML, Vertex AI, serving e cenários integrados |
| 8 | Revisão, simulados, estratégia e checklist final |

## Estrutura

```text
gcp-professional-data-engineer/
├── README.md
├── semana-01-fundamentos-arquitetura-dados/
├── semana-02-ingestao-pubsub-datastream/
├── semana-03-dataflow-dataproc-processamento/
├── semana-04-bigquery-storage-analytics/
├── semana-05-governanca-dataplex-dataform-quality/
├── semana-06-orquestracao-operacao-seguranca/
├── semana-07-ml-analytics-avancado-cenarios/
└── semana-08-revisao-simulados-estrategia/
```

## Ritmo sugerido

```text
20 min teoria
30 min lab
10 min revisão
5–6 dias/semana
```

## Regras de ouro

```text
Streaming complexo → Pub/Sub + Dataflow
Spark/Hadoop legado → Dataproc
CDC relacional → Datastream
Analytics SQL → BigQuery
ELT SQL versionado → Dataform
Governança/metadata → Dataplex
Orquestração Airflow → Composer
Wide-column baixa latência → Bigtable
SQL operacional distribuído → Spanner
```

## Critério de prontidão

```text
≥ 80% nos simulados
+
labs executados
+
erros compreendidos
+
capacidade de justificar arquitetura
```
