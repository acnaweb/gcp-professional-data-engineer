# Aula 4 — Estratégia de Prova

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
- Treinar eliminação e tempo.

# Conceito
No exame padrão atual, 40–50 questões em 2h. Gerencie tempo e leia restrições antes de serviços.

## Arquitetura construída
```text
requirement → constraints → pattern → eliminate → choose
```

# Criar
```bash
cat > card.txt <<'EOF'
CDC=Datastream
EventWindow=Dataflow
Spark=Dataproc
SQLDAG=Dataform
Airflow=Composer
Analytics=BigQuery
WideColumn=Bigtable
DistributedSQL=Spanner
Governance=Dataplex
EOF
```

# Inspecionar
```bash
cat card.txt
```

# Testar
Para cada linha, escreva uma condição que mudaria a resposta.

# Quebrar propositalmente
Aplique “sempre escolha o mais gerenciado” sem ler requisito.

# Troubleshooting
**Sintoma:** resposta moderna mas incompatível. **Causa:** heurística virou dogma. **Correção:** requisito explícito vence preferência.

# Corrigir
Treine ~2–2,5 min/questão e reserve revisão.

# Serviços semelhantes e critérios de escolha
Managed-first é heurística; existing tech, compliance, latency e migration cost podem mudar escolha.

# Pegadinhas e decisões típicas da prova
“Existing Spark”, “event time”, “no copy”, “least ops” são pistas fortes.

# Questões estilo Professional Data Engineer
1. No-copy sharing: export CSV atende?
2. Existing Spark: Dataflow por ser serverless?
3. Heurística pode superar requisito?

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
rm -f card.txt
```
