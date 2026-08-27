# Aula 5 — Simulado Temático

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
- Praticar 10 decisões com justificativa.

# Conceito
A prova Professional cobra a melhor escolha sob restrições, não apenas uma solução possível.

## Arquitetura construída
```text
question → requirement → eliminate → trade-off → answer
```

# Criar
```bash
cat > errors.md <<'EOF'
|Tema|Erro|Regra correta|
|---|---|---|
EOF
```

# Inspecionar
```bash
cat errors.md
```

# Testar
Responda: 1 event time? 2 Spark? 3 CDC? 4 SQL DAG? 5 Airflow? 6 wide-column? 7 distributed SQL? 8 metadata? 9 PII column? 10 hot key?

# Quebrar propositalmente
Escolha Dataflow para Spark legado de propósito.

# Troubleshooting
**Sintoma:** solução poderia processar, mas exige reescrita. **Hipótese:** ignorou existing Spark. **Evidência:** requisito. **Causa:** escolha por capacidade genérica. **Correção:** Dataproc.

# Corrigir
Gabarito: Dataflow, Dataproc, Datastream, Dataform, Composer, Bigtable, Spanner, Dataplex, policy tags, rekey/fanout.

# Serviços semelhantes e critérios de escolha
Compare pares: Dataflow/Dataproc; Dataform/Composer; BigQuery/Spanner/Bigtable.

# Pegadinhas e decisões típicas da prova
A pista está frequentemente em “existing”, “least operational overhead”, “no copy”, “event time”, “global relational”.

# Questões estilo Professional Data Engineer
1. Meta de acerto sugerida?
2. Justificativa importa?
3. Erro recorrente num par de serviços: o que revisar?

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
rm -f errors.md
```
