# Google Cloud Professional Data Engineer — Roadmap Prático 2026

Roadmap prático de preparação para a certificação **Google Cloud Professional Data Engineer**, estruturado em **8 semanas** com foco em:

- Engenharia de Dados no Google Cloud;
- arquitetura de dados;
- ingestão batch, streaming e CDC;
- processamento distribuído;
- BigQuery;
- Dataflow;
- Pub/Sub;
- Dataproc;
- Datastream;
- Dataform;
- Dataplex;
- Cloud Composer;
- qualidade e governança;
- segurança;
- observabilidade;
- performance;
- confiabilidade;
- FinOps;
- troubleshooting;
- decisões arquiteturais;
- preparação para o exame.

A proposta deste repositório não é apenas explicar produtos.

O objetivo é desenvolver a capacidade de:

```text
Entender
   ↓
Construir
   ↓
Inspecionar
   ↓
Testar
   ↓
Falhar
   ↓
Diagnosticar
   ↓
Corrigir
   ↓
Operar
   ↓
Escolher
```

---

# Certificação

A certificação **Professional Data Engineer** valida a capacidade de projetar, construir, implantar, monitorar, manter, otimizar e proteger workloads complexos de dados no Google Cloud.

O exame avalia principalmente a capacidade de:

```text
Design data processing systems
        ↓
Ingest and process data
        ↓
Store data
        ↓
Prepare and use data for analysis
        ↓
Maintain and automate data workloads
```

---

# Formato Atual do Exame

No exame padrão:

```text
Duração
→ 2 horas

Questões
→ 40–50

Formato
→ Multiple Choice
→ Multiple Select

Idiomas
→ Inglês
→ Japonês

Taxa
→ US$ 200 + impostos aplicáveis

Pré-requisitos
→ Nenhum

Experiência recomendada
→ 3+ anos na indústria
→ incluindo 1+ ano projetando e gerenciando soluções no Google Cloud
```

> Consulte sempre a página oficial antes de agendar, pois o Google Cloud pode atualizar o conteúdo, produtos e formato do exame.

---

# Princípio Central deste Roadmap

A prova Professional Data Engineer exige muito mais do que memorizar:

```text
Pub/Sub
Dataflow
Dataproc
BigQuery
Datastream
Dataplex
```

Ela espera que você consiga responder:

```text
Qual serviço?
Por quê?
Qual padrão?
Qual trade-off?
Qual custo?
Qual impacto operacional?
Como proteger?
Como observar?
Como recuperar?
```

Por isso:

> **Dê prioridade a cenários de decisão arquitetural e trade-offs, pois a prova Professional Data Engineer cobra mais “qual serviço/padrão escolher e por quê” do que simples memorização de comandos.**

---

# Padrão Prático das Aulas

Todas as aulas deste repositório seguem o mesmo fluxo:

```text
Conceito
   ↓
Criar
   ↓
Inspecionar
   ↓
Testar
   ↓
Quebrar propositalmente
   ↓
Troubleshooting
   ↓
Corrigir
   ↓
Questões estilo prova
   ↓
Cleanup
```

O objetivo é não estudar um serviço apenas pelo caminho feliz.

Cada laboratório procura responder:

```text
Como criar?
Como verificar?
Como saber se está funcionando?
Como ele quebra?
Como identificar a causa?
Como corrigir?
Quando eu deveria escolher outro serviço?
```

---

# Modelo de Troubleshooting

Ao encontrar uma falha, use sempre:

```text
Sintoma
   ↓
Hipótese
   ↓
Evidência
   ↓
Causa
   ↓
Correção
```

Exemplo:

```text
Sintoma
Dataflow backlog crescendo

        ↓

Hipótese
Pipeline não acompanha a taxa de entrada

        ↓

Evidência
Backlog ↑
Data freshness ↑
Worker CPU desigual

        ↓

Causa
Hot key

        ↓

Correção
Rekey / Fanout / Salting
```

Evite o padrão:

```text
Problema
   ↓
Adicionar mais máquinas
```

A certificação Professional normalmente valoriza a **correção da causa**, não apenas aumento de recursos.

---

# Case Contínuo — RetailPulse

Ao longo das oito semanas construiremos e evoluiremos uma plataforma fictícia de e-commerce chamada:

```text
RetailPulse
```

A empresa possui:

```text
Pedidos
Clientes
Produtos
Eventos de navegação
Banco transacional
Aplicações
Analytics
Machine Learning
```

Inicialmente:

```text
CSV
 ↓
Cloud Storage
 ↓
BigQuery
```

Depois evoluiremos para:

```text
                            ┌───────────────┐
                            │ Applications  │
                            └───────┬───────┘
                                    │
                                    ▼
                               Pub/Sub
                                    │
                                    ▼
                                Dataflow
                                    │
                                    ▼
Operational DB ── CDC ── Datastream ──────► BigQuery
                                    ▲
                                    │
Cloud Storage ──────────────────────┘
                                    │
                                    ▼
                                 Dataform
                                    │
                                    ▼
                         Trusted / Analytics
                                    │
                  ┌─────────────────┼────────────────┐
                  ▼                 ▼                ▼
               Looker           BigQuery ML      Vertex AI
```

Transversalmente:

```text
Dataplex
IAM
Data Quality
Lineage
Cloud Logging
Cloud Monitoring
Cloud Composer
Cost Management
```

---

# Roadmap de 8 Semanas

## Semana 1 — Fundamentos, Arquitetura e Modelagem de Dados

Objetivo:

> Construir a base arquitetural da plataforma e desenvolver o raciocínio de seleção de serviços.

Conteúdo:

- arquitetura de Data Platform;
- exam guide;
- requisitos funcionais e não funcionais;
- batch x streaming x CDC;
- OLTP x OLAP;
- modelagem analítica;
- partitioning;
- clustering;
- segurança;
- IAM;
- governança;
- RPO;
- RTO;
- idempotência;
- retries;
- DLQ;
- laboratório end-to-end.

Pasta:

```text
semana-01-fundamentos-arquitetura-dados/
```

---

## Semana 2 — Ingestão, Pub/Sub, Streaming e Datastream

Objetivo:

> Dominar padrões de ingestão e entender como escolher entre eventos, arquivos, streaming e CDC.

Conteúdo:

- Pub/Sub;
- topics;
- subscriptions;
- ACK;
- retention;
- fan-out;
- redelivery;
- ordering;
- event time;
- processing time;
- windows;
- watermark;
- late data;
- Datastream;
- CDC;
- backfill;
- Storage Transfer Service;
- Transfer Appliance;
- Pub/Sub → BigQuery.

Pasta:

```text
semana-02-ingestao-pubsub-datastream/
```

---

## Semana 3 — Dataflow, Beam, Dataproc e Processamento

Objetivo:

> Trabalhar processamento distribuído batch e streaming e entender quando usar cada engine.

Conteúdo:

- Apache Beam;
- PCollection;
- transforms;
- Dataflow;
- batch;
- streaming;
- backlog;
- data freshness;
- hot keys;
- autoscaling;
- Dataproc;
- Spark;
- Hadoop;
- Serverless for Apache Spark;
- BigQuery ELT;
- MERGE;
- idempotência;
- Dataflow templates.

Pasta:

```text
semana-03-dataflow-dataproc-processamento/
```

---

## Semana 4 — BigQuery, Storage, Performance e Analytics

Objetivo:

> Dominar o principal mecanismo analítico do Google Cloud e seus trade-offs de performance e custo.

Conteúdo:

- arquitetura BigQuery;
- columnar storage;
- distributed execution;
- slots;
- partitioning;
- clustering;
- query plan;
- INFORMATION_SCHEMA;
- performance;
- bytes processed;
- reservations;
- capacity;
- authorized views;
- row-level security;
- column-level security;
- data sharing;
- external tables;
- BigLake;
- loading;
- streaming;
- partition filter.

Pasta:

```text
semana-04-bigquery-storage-analytics/
```

---

## Semana 5 — Dataplex, Dataform, Data Quality e Lineage

Objetivo:

> Transformar dados em produtos governados e confiáveis.

Conteúdo:

- Dataplex;
- Universal Catalog;
- metadata;
- data ownership;
- Dataform;
- SQLX;
- dependencies;
- assertions;
- incremental models;
- data quality;
- completeness;
- validity;
- uniqueness;
- consistency;
- timeliness;
- quarantine;
- lineage;
- schema evolution;
- impact analysis.

Pasta:

```text
semana-05-governanca-dataplex-dataform-quality/
```

---

## Semana 6 — Orquestração, Operação e Segurança

Objetivo:

> Operar pipelines de dados com confiabilidade, segurança e observabilidade.

Conteúdo:

- Cloud Composer;
- Apache Airflow;
- DAG;
- tasks;
- retries;
- scheduling;
- Workflows;
- Cloud Scheduler;
- Cloud Monitoring;
- Cloud Logging;
- log-based metrics;
- freshness;
- backlog;
- Service Accounts;
- IAM;
- VPC Service Controls;
- CMEK;
- private networking;
- quality gates;
- orquestração integrada.

Pasta:

```text
semana-06-orquestracao-operacao-seguranca/
```

---

## Semana 7 — ML, Serving e Cenários Integrados

Objetivo:

> Integrar Engenharia de Dados com Machine Learning, serving e data products.

Conteúdo:

- BigQuery ML;
- Vertex AI;
- feature preparation;
- feature freshness;
- training-serving skew;
- analytics store x serving store;
- Reverse ETL;
- batch activation;
- Spanner;
- Bigtable;
- Cloud SQL;
- arquitetura orientada a requisitos;
- trade-offs;
- cenários integrados;
- simulado temático.

Pasta:

```text
semana-07-ml-analytics-avancado-cenarios/
```

---

## Semana 8 — Revisão, Simulados e Estratégia de Prova

Objetivo:

> Consolidar conhecimento e transformar conteúdo técnico em capacidade de decisão rápida.

Conteúdo:

- revisão por domínio;
- simulados;
- troubleshooting;
- arquitetura;
- trade-offs;
- gestão de tempo;
- eliminação de alternativas;
- checklist final;
- readiness assessment;
- estratégia para prova.

Pasta:

```text
semana-08-revisao-simulados-estrategia/
```

---

# Estrutura do Repositório

```text
gcp-professional-data-engineer/
│
├── README.md
│
├── semana-01-fundamentos-arquitetura-dados/
│   ├── README.md
│   ├── aula-01-exam-guide-arquitetura-data-platform.md
│   ├── aula-02-modelagem-dados-oltp-olap.md
│   ├── aula-03-seguranca-governanca-compliance.md
│   ├── aula-04-confiabilidade-sla-dr.md
│   └── aula-05-lab-arquitetura-end-to-end.md
│
├── semana-02-ingestao-pubsub-datastream/
│   ├── README.md
│   ├── aula-01-pubsub-fundamentos.md
│   ├── aula-02-streaming-patterns.md
│   ├── aula-03-datastream-cdc.md
│   ├── aula-04-transfer-services.md
│   └── aula-05-lab-pubsub-bigquery.md
│
├── semana-03-dataflow-dataproc-processamento/
│   ├── README.md
│   ├── aula-01-dataflow-beam.md
│   ├── aula-02-dataflow-streaming.md
│   ├── aula-03-dataproc-spark.md
│   ├── aula-04-bigquery-transformacoes.md
│   └── aula-05-lab-dataflow-template.md
│
├── semana-04-bigquery-storage-analytics/
│   ├── README.md
│   ├── aula-01-bigquery-architecture.md
│   ├── aula-02-bigquery-performance-cost.md
│   ├── aula-03-bigquery-security-sharing.md
│   ├── aula-04-bigquery-streaming-loading.md
│   └── aula-05-lab-partition-cluster.md
│
├── semana-05-governanca-dataplex-dataform-quality/
│   ├── README.md
│   ├── aula-01-dataplex-governanca.md
│   ├── aula-02-dataform-elt.md
│   ├── aula-03-data-quality.md
│   ├── aula-04-data-catalog-lineage.md
│   └── aula-05-lab-dataform.md
│
├── semana-06-orquestracao-operacao-seguranca/
│   ├── README.md
│   ├── aula-01-composer-airflow.md
│   ├── aula-02-workflows-scheduler.md
│   ├── aula-03-monitoring-data-workloads.md
│   ├── aula-04-security-networking-data.md
│   └── aula-05-lab-orquestracao.md
│
├── semana-07-ml-analytics-avancado-cenarios/
│   ├── README.md
│   ├── aula-01-bigquery-ml.md
│   ├── aula-02-vertex-ai-integration.md
│   ├── aula-03-reverse-etl-serving.md
│   ├── aula-04-cenarios-integrados.md
│   └── aula-05-simulado-tematico.md
│
└── semana-08-revisao-simulados-estrategia/
    ├── README.md
    ├── aula-01-revisao-dominios.md
    ├── aula-02-simulado-01.md
    ├── aula-03-simulado-02.md
    ├── aula-04-estrategia-prova.md
    └── aula-05-checklist-final.md
```

---

# Serviços Mais Importantes

## Ingestão

```text
Pub/Sub
Datastream
Storage Transfer Service
Transfer Appliance
Cloud Storage
```

---

## Processamento

```text
Dataflow
Apache Beam
Dataproc
Apache Spark
BigQuery
```

---

## Storage

```text
Cloud Storage
BigQuery
Bigtable
Spanner
Cloud SQL
AlloyDB
```

---

## Transformação

```text
BigQuery SQL
Dataform
Dataflow
Dataproc
```

---

## Governança

```text
Dataplex
IAM
Policy Tags
Data Quality
Lineage
Audit Logs
```

---

## Orquestração

```text
Cloud Composer
Apache Airflow
Workflows
Cloud Scheduler
```

---

## Observabilidade

```text
Cloud Monitoring
Cloud Logging
Log-based Metrics
Data Freshness
Backlog
Job Metrics
```

---

# Modelos Mentais para a Prova

## Ingestão

```text
Arquivos
→ Cloud Storage / Load Jobs

Eventos
→ Pub/Sub

CDC
→ Datastream

Transferência de object storage
→ Storage Transfer Service

Transferência offline massiva
→ Transfer Appliance
```

---

## Processamento

```text
SQL Analytics / ELT
→ BigQuery

SQL DAG versionada
→ Dataform

Beam / batch + streaming
→ Dataflow

Spark / Hadoop
→ Dataproc
```

---

## Storage

```text
Object Storage
→ Cloud Storage

Analytics
→ BigQuery

Wide-column / baixa latência / grande throughput
→ Bigtable

SQL distribuído global
→ Spanner

Relacional tradicional
→ Cloud SQL / AlloyDB
```

---

## Orquestração

```text
Airflow / Data DAG complexa
→ Composer

APIs / serviços
→ Workflows

Cron simples
→ Cloud Scheduler

SQL dependencies
→ Dataform
```

---

# Comparações que Você Precisa Dominar

```text
Dataflow x Dataproc

Pub/Sub x Datastream

BigQuery x Spanner

BigQuery x Bigtable

Cloud SQL x AlloyDB x Spanner

Dataform x Composer

Composer x Workflows

Native BigQuery Table x External Table

Partitioning x Clustering

IAM x VPC Service Controls

Row-level x Column-level Security

Batch x Streaming x CDC

Analytics Store x Serving Store
```

Não memorize apenas:

```text
Produto A faz X
Produto B faz Y
```

Estude:

```text
Quando A é melhor?
Quando B é melhor?
Qual requisito muda a decisão?
Qual custo operacional?
Qual limite?
Qual impacto de escala?
```

---

# Pegadinhas Frequentes

## Dataflow

```text
Backlog crescendo
+
Data freshness crescendo
```

Não significa automaticamente:

```text
adicionar workers
```

Pode ser:

```text
Hot key
Sink throttling
Quota
Bad record
Stage bottleneck
```

---

## Pub/Sub

```text
Redelivery
```

não significa necessariamente falha do serviço.

Pode significar:

```text
ACK ausente
ACK deadline expirado
consumer lento
```

---

## BigQuery

```text
LIMIT 10
```

não significa:

```text
ler apenas 10 linhas
```

Partition pruning e seleção de colunas são mais importantes.

---

## IAM

```text
403
```

não significa automaticamente:

```text
dar Owner
```

Investigue:

```text
IAM
API
Service Account
Impersonation
VPC Service Controls
Resource scope
```

---

## Datastream

```text
CDC
≠
SELECT * a cada minuto
```

CDC é baseado em:

```text
transaction logs
WAL
binlog
replication
```

---

# Ferramentas Utilizadas

```text
gcloud
bq
SQL
Python
Apache Beam
Spark
Airflow
Dataform SQLX
bash
Cloud Shell
Google Cloud Console
```

---

# Rotina de Estudo Sugerida

Por sessão:

```text
20 min
Conceito e arquitetura

        ↓

30–40 min
Laboratório

        ↓

15 min
Falha + Troubleshooting

        ↓

10 min
Questões estilo prova

        ↓

5 min
Anotações / cleanup
```

Ritmo:

```text
5–6 dias por semana
8 semanas
```

---

# Como Estudar cada Aula

Não execute os comandos mecanicamente.

Antes de cada comando pergunte:

```text
O que este comando vai criar?
Onde o recurso será criado?
Qual identidade será usada?
Qual API precisa estar habilitada?
Quanto isso pode custar?
Como verifico depois?
Como removo?
```

Depois do laboratório:

```text
Consigo explicar a arquitetura sem olhar?
Consigo recriar?
Consigo encontrar o recurso?
Consigo provocar uma falha?
Consigo diagnosticar?
Consigo justificar por que escolhi esse serviço?
```

---

# Estratégia para Simulados

Para cada erro registre:

```text
Questão:
Domínio:
Requisito principal:
Resposta marcada:
Resposta correta:
Motivo do erro:
Serviço confundido:
Regra arquitetural:
```

Classifique o erro:

```text
Conceito
Leitura do requisito
Serviços semelhantes
Troubleshooting
Segurança
Performance
Custo
```

O objetivo do simulado não é apenas:

```text
tirar 85%
```

É identificar:

```text
por que os outros 15% estavam errados
```

---

# Critério de Prontidão

Uma boa referência:

```text
≥ 80% nos simulados
+
labs executados
+
erros compreendidos
+
troubleshooting causal
+
trade-offs claros
```

Você deve conseguir olhar para um cenário e responder:

```text
Qual padrão de dados?
Batch?
Streaming?
CDC?

Qual latência?
Qual volume?
Qual SLA?
Qual segurança?
Qual governança?
Qual operação?
Qual custo?
```

E então escolher a arquitetura.

---

# Checklist Técnico Final

## Ingestão

- [ ] Pub/Sub
- [ ] Topics
- [ ] Subscriptions
- [ ] ACK
- [ ] Redelivery
- [ ] Retention
- [ ] Ordering
- [ ] Datastream
- [ ] CDC
- [ ] Backfill
- [ ] Storage Transfer Service
- [ ] Transfer Appliance

## Streaming

- [ ] Event Time
- [ ] Processing Time
- [ ] Windows
- [ ] Watermark
- [ ] Late Data
- [ ] Backlog
- [ ] Freshness
- [ ] Hot Key

## Dataflow

- [ ] Apache Beam
- [ ] PCollection
- [ ] Transforms
- [ ] Batch
- [ ] Streaming
- [ ] Autoscaling
- [ ] Templates
- [ ] Worker Logs
- [ ] Troubleshooting

## Dataproc

- [ ] Spark
- [ ] Hadoop
- [ ] Cluster
- [ ] Serverless Spark
- [ ] Ephemeral Workloads
- [ ] GCS Integration

## BigQuery

- [ ] Dataset
- [ ] Tables
- [ ] Partitioning
- [ ] Clustering
- [ ] INFORMATION_SCHEMA
- [ ] Query Plan
- [ ] Slots
- [ ] Reservations
- [ ] External Tables
- [ ] Streaming
- [ ] MERGE
- [ ] Authorized Views
- [ ] Row-level Security
- [ ] Column-level Security

## Governance

- [ ] Dataplex
- [ ] Catalog
- [ ] Metadata
- [ ] Lineage
- [ ] Data Quality
- [ ] Ownership
- [ ] Policy Tags

## Transformation

- [ ] Dataform
- [ ] SQLX
- [ ] ref()
- [ ] Assertions
- [ ] Incremental Models

## Orchestration

- [ ] Composer
- [ ] Airflow
- [ ] DAG
- [ ] Tasks
- [ ] Retry
- [ ] Workflows
- [ ] Scheduler

## Security

- [ ] IAM
- [ ] Service Accounts
- [ ] Least Privilege
- [ ] Impersonation
- [ ] VPC Service Controls
- [ ] CMEK
- [ ] Audit Logs

## Operations

- [ ] Monitoring
- [ ] Logging
- [ ] Alerts
- [ ] Freshness
- [ ] Backlog
- [ ] Quotas
- [ ] Cost
- [ ] Idempotency
- [ ] Retry
- [ ] DLQ

---

# Cleanup

Um Data Engineer também precisa pensar em custo.

Ao finalizar cada laboratório:

```text
Verifique
   ↓
Liste recursos
   ↓
Confirme o que ainda está ativo
   ↓
Remova o que não será reutilizado
```

Comandos úteis:

```bash
gcloud pubsub topics list

gcloud dataflow jobs list \
  --region=us-central1

gcloud dataproc clusters list \
  --region=us-central1

gcloud workflows list \
  --location=us-central1

gcloud storage buckets list

bq ls
```

Nunca execute cleanup global sem verificar o projeto e os recursos.

---

# Meta Final

Ao concluir este roadmap, o objetivo não é conseguir apenas responder:

> “O que é Dataflow?”

Você deve conseguir responder:

```text
Por que Dataflow?
Por que não Dataproc?
Por que não BigQuery SQL?
Qual source?
Qual sink?
Qual semântica de entrega?
Qual latência?
Qual custo?
Qual falha provável?
Como observar?
Como recuperar?
```

Da mesma forma:

```text
Por que Pub/Sub?
Por que Datastream?

Por que BigQuery?
Por que Bigtable?
Por que Spanner?

Por que Dataform?
Por que Composer?

Por que batch?
Por que streaming?
Por que CDC?
```

Esse é o nível de raciocínio esperado de um:

# Google Cloud Professional Data Engineer

---

# Referências Oficiais

Use como fonte principal:

- Google Cloud Professional Data Engineer Certification
- Professional Data Engineer Exam Guide
- Google Cloud Documentation
- Google Cloud Architecture Center
- Google Cloud Skills / Learning Path

O conteúdo do exame e os produtos do Google Cloud evoluem continuamente.

Sempre valide informações críticas na documentação oficial.

---

# Licença / Uso

Este repositório é um material de estudo independente, criado para apoiar preparação prática para a certificação.

Google Cloud, BigQuery, Dataflow, Dataproc, Pub/Sub, Vertex AI e demais produtos citados são marcas e serviços do Google.

Este material não representa conteúdo oficial do exame nem possui associação com o Google.
