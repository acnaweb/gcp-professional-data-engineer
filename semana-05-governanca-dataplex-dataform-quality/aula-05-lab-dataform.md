# Aula 5 — Lab Dataform

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
- Consolidar DAG SQL e assertion.
- Quebrar quality gate.

# Conceito
Dataform permite transformar e testar no mesmo grafo lógico. Quality gate deve ser parte do release de dados.

## Arquitetura construída
```text
raw → stg → fact → assertion
```

# Criar
```bash
mkdir -p df_case/definitions
printf 'config { type: "view" }\nSELECT * FROM ${ref("raw_orders")}\n' > df_case/definitions/stg.sqlx
printf 'config { type: "table" }\nSELECT * FROM ${ref("stg")} WHERE amount IS NOT NULL\n' > df_case/definitions/fact.sqlx
printf 'config { type: "assertion" }\nSELECT * FROM ${ref("fact")} WHERE amount<0\n' > df_case/definitions/assert.sqlx
```

# Inspecionar
```bash
find df_case -type f -print; grep -R 'ref(' df_case
```

# Testar
Desenhe dependências a partir dos refs.

# Quebrar propositalmente
Remova `WHERE amount IS NOT NULL` do fact.

# Troubleshooting
**Sintoma:** bad data pode avançar. **Hipótese:** gate upstream removido. **Evidência:** SQLX diff + assertion. **Causa:** mudança regressiva. **Correção:** restaurar e usar CI/compilation/assertions.

# Corrigir
Restaure filtro.

# Serviços semelhantes e critérios de escolha
Dataform para SQL graph; Composer/Workflows para orquestração acima dele.

# Pegadinhas e decisões típicas da prova
Assertion vazia=sucesso; linhas retornadas=violações. Incremental deve ser idempotente.

# Questões estilo Professional Data Engineer
1. Assertion retorna 8 linhas: significa?
2. Dataform precisa chamar Spark e API: suficiente sozinho?
3. Incremental reexecuta: qual preocupação?

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
rm -rf df_case
```
