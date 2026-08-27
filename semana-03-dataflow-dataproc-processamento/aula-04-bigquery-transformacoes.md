# Aula 4 — Transformações no BigQuery

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
- Criar MERGE idempotente.
- Provocar duplicação com INSERT.

# Conceito
MERGE suporta upsert incremental. Reprocessamento deve produzir estado consistente.

## Arquitetura construída
```text
staging → MERGE → target
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_merge" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_merge.t` AS SELECT "o1" id,100 amount;
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_merge.s` AS SELECT "o1" id,120 amount UNION ALL SELECT "o2",80;'
bq query --use_legacy_sql=false '
MERGE `'"${PROJECT_ID}"'.retail_merge.t` T USING `'"${PROJECT_ID}"'.retail_merge.s` S ON T.id=S.id
WHEN MATCHED THEN UPDATE SET amount=S.amount
WHEN NOT MATCHED THEN INSERT(id,amount) VALUES(S.id,S.amount);'
```

# Inspecionar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_merge.t` ORDER BY id'
```

# Testar
Execute MERGE novamente e conte IDs.

# Quebrar propositalmente
Execute `INSERT INTO t SELECT * FROM s` duas vezes.

# Troubleshooting
**Sintoma:** duplicatas. **Hipótese:** carga não idempotente. **Evidência:** `GROUP BY id HAVING COUNT(*)>1`. **Causa:** INSERT cego. **Correção:** MERGE/dedup/partition overwrite.

# Corrigir
Reconstrua target com `ROW_NUMBER()` ou reaplique estratégia idempotente.

# Serviços semelhantes e critérios de escolha
MERGE=upsert; INSERT=append; partition overwrite=reprocessar partição; Dataform incremental=SQL DAG versionada.

# Pegadinhas e decisões típicas da prova
MERGE sem filtro pode ser caro. Idempotência é requisito operacional.

# Questões estilo Professional Data Engineer
1. Updates+inserts: qual SQL?
2. Retry duplica linhas: qual propriedade faltou?
3. Partição diária inteira recalculada: alternativa ao MERGE linha a linha?

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
bq rm -r -f "${PROJECT_ID}:retail_merge"
```
