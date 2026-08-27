# Aula 4 — Metadata, Catalog e Lineage

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
- Criar upstream/view downstream.
- Quebrar schema e usar lineage mental.

# Conceito
Lineage mostra dependências e impacto; audit logs mostram ações; monitoring mostra runtime; catalog mostra contexto/descoberta.

## Arquitetura construída
```text
raw → view/model → dashboard
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_lineage" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_lineage.raw` AS SELECT "o1" id,10 amount;
CREATE OR REPLACE VIEW `'"${PROJECT_ID}"'.retail_lineage.v` AS SELECT id,amount FROM `'"${PROJECT_ID}"'.retail_lineage.raw`;'
```

# Inspecionar
```bash
bq query --use_legacy_sql=false 'SELECT table_name,table_type FROM `'"${PROJECT_ID}"'.retail_lineage.INFORMATION_SCHEMA.TABLES`'
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_lineage.v`'
```

# Quebrar propositalmente
Recrie `raw` com coluna `total_amount` no lugar de `amount`, depois consulte a view.

# Troubleshooting
**Sintoma:** view quebra. **Hipótese:** breaking schema change. **Evidência:** schemas upstream/downstream. **Causa:** rename não coordenado. **Correção:** compatibilidade/versionamento/atualização guiada por lineage.

# Corrigir
Atualize view usando `total_amount AS amount`.

# Serviços semelhantes e critérios de escolha
Catalog=descoberta; lineage=dependência; audit=quem fez; monitoring=estado.

# Pegadinhas e decisões típicas da prova
Modified time não é freshness semântica. Audit não é lineage.

# Questões estilo Professional Data Engineer
1. Quem deletou tabela: onde olhar?
2. Quem depende desta coluna: qual conceito?
3. Owner/classificação: onde?

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
bq rm -r -f "${PROJECT_ID}:retail_lineage"
```
