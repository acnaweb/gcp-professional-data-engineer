# Aula 1 — Exam Guide e Arquitetura de Data Platform

## Objetivos
- Entender os cinco domínios do exame.
- Revisar arquitetura de dados no Google Cloud.
- Relacionar requisitos de negócio, SLA, compliance, custo e escalabilidade.

## Domínios oficiais
```text
1. Design data processing systems
2. Ingest and process data
3. Store data
4. Prepare and use data for analysis
5. Maintain and automate data workloads
```

## Modelo de arquitetura
```text
Sources
  ↓
Ingestion
  ↓
Storage
  ↓
Processing
  ↓
Serving / Analytics / ML
  ↓
Governance + Security + Observability
```

## Serviços principais
| Camada | Serviços |
|---|---|
| Ingestão | Pub/Sub, Datastream, Storage Transfer |
| Processamento | Dataflow, Dataproc, BigQuery |
| Storage | Cloud Storage, BigQuery, Bigtable, Spanner |
| Orquestração | Cloud Composer, Workflows |
| Governança | Dataplex, IAM, DLP |
| Analytics | BigQuery, Looker |
| ML | Vertex AI, BigQuery ML |

## Laboratório
1. Liste projetos e APIs.
2. Habilite BigQuery, Pub/Sub e Dataflow.
3. Crie um dataset de laboratório.

```bash
gcloud services enable bigquery.googleapis.com pubsub.googleapis.com dataflow.googleapis.com
bq mk --dataset ${GOOGLE_CLOUD_PROJECT}:pde_lab
```

## Questões estilo PDE
1. O requisito mais importante ao escolher serviço é tecnologia preferida da equipe?  
**Não.** Priorize requisitos funcionais, SLA, escala, segurança, custo e operação.

2. Serviço serverless para analytics em grande escala?  
**BigQuery.**

## Checklist
- [ ] Sei descrever uma data platform no GCP
- [ ] Conheço os cinco domínios oficiais
- [ ] Sei mapear requisitos para serviços
