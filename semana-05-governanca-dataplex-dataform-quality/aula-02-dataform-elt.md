# Aula 2 — Dataform para ELT

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
- Criar SQLX, refs e assertion.
- Quebrar referência.

# Conceito
Dataform organiza transformações SQL no BigQuery com dependências, versionamento, assertions e incremental models.

## Arquitetura construída
```text
raw → stg(ref) → fact → assertion
```

# Criar
```bash
mkdir -p df_lab/definitions
cat > df_lab/definitions/stg.sqlx <<'EOF'
config { type: "table" }
SELECT * FROM ${ref("raw_orders")}
EOF
cat > df_lab/definitions/assert.sqlx <<'EOF'
config { type: "assertion" }
SELECT * FROM ${ref("stg")} WHERE order_id IS NULL
EOF
```

# Inspecionar
```bash
grep -R 'ref(' df_lab/definitions
```

# Testar
Liste DAG por regex/referências.

# Quebrar propositalmente
```bash
cp df_lab/definitions/stg.sqlx /tmp/stg.good
sed -i 's/raw_orders/raw_DOES_NOT_EXIST/' df_lab/definitions/stg.sqlx
```

# Troubleshooting
**Sintoma:** compilation/dependency error. **Hipótese:** `ref` inexistente. **Evidência:** nomes das actions. **Causa:** rename sem dependentes. **Correção:** restaurar referência e compilar no CI.

# Corrigir
```bash
cp /tmp/stg.good df_lab/definitions/stg.sqlx
```

# Serviços semelhantes e critérios de escolha
Scheduled Query=isolada; Dataform=SQL DAG; Composer=orquestração multi-serviço; Dataflow=stream/transform não SQL.

# Pegadinhas e decisões típicas da prova
Assertion que retorna linhas = violações. Não use Composer para reinventar DAG SQL simples.

# Questões estilo Professional Data Engineer
1. 100 modelos SQL dependentes: qual?
2. Uma query diária isolada: precisa Dataform?
3. Dataflow + API + Dataform: quem orquestra?

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
rm -rf df_lab /tmp/stg.good
```
