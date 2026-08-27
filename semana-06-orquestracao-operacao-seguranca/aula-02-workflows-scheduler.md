# Aula 2 — Workflows e Cloud Scheduler

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
- Criar workflow real.
- Quebrar variável e inspecionar executions.

# Conceito
Workflows orquestra APIs/serviços sem ambiente Airflow; Scheduler dispara cron.

## Arquitetura construída
```text
Scheduler → Workflow → API steps
```

# Criar
```bash
gcloud services enable workflows.googleapis.com workflowexecutions.googleapis.com
cat > wf.yaml <<'EOF'
main:
  params: [args]
  steps:
    - hello:
        return: ${"hello " + default(map.get(args,"name"),"Retail")}
EOF
gcloud workflows deploy retail-hello --location="${REGION}" --source=wf.yaml
```

# Inspecionar
```bash
gcloud workflows describe retail-hello --location="${REGION}"
gcloud workflows executions list retail-hello --location="${REGION}"
```

# Testar
```bash
gcloud workflows run retail-hello --location="${REGION}" --data='{"name":"PDE"}'
```

# Quebrar propositalmente
Edite YAML para retornar variável inexistente e redeploy.

# Troubleshooting
**Sintoma:** execution/deploy falha. **Hipótese:** expressão/variável. **Evidência:** execution error. **Causa:** definição inconsistente. **Correção:** restaurar YAML.

# Corrigir
Restaure `wf.yaml` e redeploy.

# Serviços semelhantes e critérios de escolha
Workflows=API flow; Composer=Airflow; Scheduler=cron.

# Pegadinhas e decisões típicas da prova
Workflows não transforma grandes dados. Scheduler não mantém DAG.

# Questões estilo Professional Data Engineer
1. Branching entre APIs: qual?
2. Airflow sensors: qual?
3. Cron simples: qual?

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
gcloud workflows delete retail-hello --location="${REGION}" --quiet; rm -f wf.yaml
```
