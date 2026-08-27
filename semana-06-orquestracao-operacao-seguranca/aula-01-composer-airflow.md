# Aula 1 — Cloud Composer e Airflow

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
- Validar DAG local.
- Criar ciclo proposital.
- Inspecionar Composer sem provisionar ambiente.

# Conceito
Composer é Airflow gerenciado. DAGs precisam ser acíclicas, idempotentes e observáveis. Ambiente Composer pode ser caro para lab curto, então pratique Airflow local + inspeção GCP.

## Arquitetura construída
```text
extract → quality → transform → publish
```

# Criar
```bash
gcloud services enable composer.googleapis.com
gcloud composer environments list --locations="${REGION}"
cat > dag_check.py <<'PY'
deps={"extract":[],"quality":["extract"],"transform":["quality"],"publish":["transform"]}
print(deps)
PY
python dag_check.py
```

# Inspecionar
```bash
gcloud composer operations list --locations="${REGION}" 2>/dev/null || true
cat dag_check.py
```

# Testar
Confirme cadeia sem ciclos.

# Quebrar propositalmente
Altere dependência para `extract` depender de `publish`.

# Troubleshooting
**Sintoma:** DAG cíclica. **Hipótese:** circular dependency. **Evidência:** grafo. **Causa:** publish→extract. **Correção:** remover ciclo. Em Composer real, inspecione DAG import errors/task logs.

# Corrigir
Restaure DAG acíclica.

# Serviços semelhantes e critérios de escolha
Composer=Airflow; Workflows=API orchestration leve; Scheduler=cron; Dataform=SQL DAG.

# Pegadinhas e decisões típicas da prova
Retry deve ser idempotente. `catchup` pode criar execuções históricas inesperadas.

# Questões estilo Professional Data Engineer
1. DAG Airflow complexa: qual?
2. Uma chamada HTTP diária: Composer obrigatório?
3. Retry de INSERT não idempotente causa?

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
rm -f dag_check.py
```
