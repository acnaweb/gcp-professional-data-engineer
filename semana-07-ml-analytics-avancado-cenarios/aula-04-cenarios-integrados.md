# Aula 4 — Cenários Integrados

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
- Criar matriz de decisão.
- Alterar requisito e reavaliar.

# Conceito
A melhor arquitetura depende de requisitos. Mudança de latência, tecnologia existente, segurança ou custo pode mudar a decisão.

## Arquitetura construída
```text
requirement change → re-evaluate → new trade-off
```

# Criar
```bash
cat > decisions.csv <<'EOF'
scenario,latency,pattern,preferred
fraud,seconds,events,PubSub+Dataflow
cdc,minutes,db_changes,Datastream+BigQuery
spark,batch,spark,Dataproc
elt,hours,sql,BigQuery+Dataform
EOF
column -s, -t decisions.csv
```

# Inspecionar
```bash
awk -F, 'NR>1{print $1,"=>",$4}' decisions.csv
```

# Testar
Justifique alternativa descartada para cada linha.

# Quebrar propositalmente
Mude ELT para `seconds,event_time_windows` mantendo BigQuery+Dataform.

# Troubleshooting
**Sintoma:** arquitetura não atende semântica/latência. **Hipótese:** requisito mudou. **Evidência:** event_time windows em segundos. **Causa:** decisão antiga. **Correção:** Pub/Sub+Dataflow+BigQuery.

# Corrigir
Atualize CSV com nova arquitetura.

# Serviços semelhantes e critérios de escolha
CDC=Datastream; event windows=Dataflow; Spark=Dataproc; SQL DAG=Dataform; Airflow=Composer.

# Pegadinhas e decisões típicas da prova
Não escolha “mais poderoso”. Existing tech e reescrita são trade-offs válidos.

# Questões estilo Professional Data Engineer
1. Spark legado+prazo curto?
2. SQL batch vira 5s windows: reavaliar para quê?
3. CDC simples: Dataflow polling ou Datastream?

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
rm -f decisions.csv
```
