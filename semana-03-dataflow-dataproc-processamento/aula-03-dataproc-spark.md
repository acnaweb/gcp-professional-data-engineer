# Aula 3 — Dataproc e Spark

## Case contínuo — RetailPulse

A RetailPulse é um e-commerce que evoluirá durante o roadmap: arquivos batch, eventos em streaming, CDC, BigQuery, qualidade, governança, orquestração, observabilidade e serving.

Use preferencialmente um projeto de laboratório:

```bash
export PROJECT_ID="$(gcloud config get-value project)"
export REGION="us-central1"
export BQ_LOCATION="US"
export BUCKET="${PROJECT_ID}-pde-retailpulse"
```

> Alguns recursos podem gerar cobrança. Recursos caros ou dependentes de Organization/integração externa são tratados com laboratório de inspeção, decisão e troubleshooting quando provisioná-los apenas para estudo não é razoável.


## Objetivos
- Inspecionar Dataproc.
- Comparar cluster e serverless Spark.
- Diagnosticar URI/IAM.

# Conceito
Dataproc executa ecossistema Spark/Hadoop com alta compatibilidade; Serverless for Apache Spark reduz gestão de cluster.

## Arquitetura construída
```text
GCS → Spark/Dataproc → GCS/BigQuery
```

# Criar
```bash
gcloud services enable dataproc.googleapis.com
gcloud dataproc clusters list --region="${REGION}"
gcloud dataproc batches list --region="${REGION}"
cat > spark_design.txt <<'EOF'
workload=pyspark_existing
runtime=ephemeral
preferred=serverless_batch
EOF
```

# Inspecionar
```bash
gcloud dataproc operations list --region="${REGION}"
cat spark_design.txt
```

# Testar
Explique por que workload efêmero não precisa cluster 24x7.

# Quebrar propositalmente
Troque `preferred=serverless_batch` por `preferred=permanent_cluster` sem requisito que justifique.

# Troubleshooting
**Sintoma:** custo ocioso alto. **Hipótese:** cluster permanente. **Evidência:** workload só roda 30 min/dia. **Causa:** lifecycle inadequado. **Correção:** ephemeral/serverless quando compatível.

# Corrigir
Restaure `serverless_batch`.

# Serviços semelhantes e critérios de escolha
Dataproc=Spark/Hadoop; Dataflow=Beam; BigQuery=SQL analytics.

# Pegadinhas e decisões típicas da prova
Dataflow não é “Spark gerenciado”. Existing Spark é pista forte para Dataproc.

# Questões estilo Professional Data Engineer
1. PySpark legado: qual serviço?
2. Job diário curto: cluster 24x7 ou efêmero?
3. Beam event-time: Dataproc ou Dataflow?

> Responda justificando **por que** a alternativa escolhida atende melhor aos requisitos e qual trade-off elimina as demais.


# Checklist final
- [ ] Entendi o conceito e os trade-offs.
- [ ] Executei o laboratório aplicável.
- [ ] Usei comandos de inspeção/list/describe.
- [ ] Provoquei a falha proposta.
- [ ] Segui sintoma → hipótese → evidência → causa → correção.
- [ ] Sei escolher entre serviços semelhantes.
- [ ] Executei ou revisei o cleanup.

# Cleanup
```bash
rm -f spark_design.txt
```
