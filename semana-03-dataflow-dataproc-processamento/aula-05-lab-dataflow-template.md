# Aula 5 — Dataflow Template e Operação

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
- Preparar staging.
- Diagnosticar temp location/API.
- Evitar custo desnecessário.

# Conceito
Templates reduzem código para padrões conhecidos. Dataflow exige APIs, staging/temp, IAM, rede e quota adequados.

## Arquitetura construída
```text
GCS input → Dataflow template → sink
```

# Criar
```bash
gcloud services enable dataflow.googleapis.com compute.googleapis.com storage.googleapis.com logging.googleapis.com
export DFB="${PROJECT_ID}-pde-dataflow"
gcloud storage buckets create "gs://${DFB}" --location="${REGION}"
gcloud dataflow jobs list --region="${REGION}"
```

# Inspecionar
```bash
gcloud storage buckets describe "gs://${DFB}"
gcloud services list --enabled --filter='config.name:dataflow.googleapis.com'
```

# Testar
Valide `gs://${DFB}/temp` como temp location lógica.

# Quebrar propositalmente
Use `gs://${PROJECT_ID}-missing/temp` como temp location em uma submissão hipotética.

# Troubleshooting
**Sintoma:** job não inicia/staging falha. **Hipóteses:** bucket, APIs, IAM, quota, região. **Evidência:** services list, storage list, job logs. **Correção:** temp válido e identidade correta.

# Corrigir
Use `gs://${DFB}/temp`.

# Serviços semelhantes e critérios de escolha
Template quando padrão atende; Flex Template para pipeline custom empacotado; BigQuery SQL para transformação puramente SQL.

# Pegadinhas e decisões típicas da prova
Mais workers não corrige config. Temp location concorrente/incorreta pode causar problemas.

# Questões estilo Professional Data Engineer
1. Template pronto atende requisito: escrever pipeline do zero?
2. Staging bucket inexistente: tipo de falha?
3. SQL group by em BQ: Dataflow obrigatório?

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
gcloud storage rm -r "gs://${DFB}"
```
