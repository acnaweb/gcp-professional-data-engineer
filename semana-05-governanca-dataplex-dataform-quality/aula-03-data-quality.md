# Aula 3 — Data Quality

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
- Criar quality score.
- Introduzir nulo/duplicata/negativo.
- Quarentenar.

# Conceito
Qualidade mede completeness, uniqueness, validity, consistency, timeliness e accuracy. Regras precisam thresholds, severidade e ação.

## Arquitetura construída
```text
raw → checks → valid→trusted / invalid→quarantine
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_quality" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_quality.raw` AS
SELECT "o1" id,10 amount UNION ALL SELECT "o2",20;'
```

# Inspecionar
```bash
bq query --use_legacy_sql=false '
SELECT COUNTIF(id IS NULL) nulls,COUNT(*)-COUNT(DISTINCT id) dups,COUNTIF(amount<0) negatives
FROM `'"${PROJECT_ID}"'.retail_quality.raw`;'
```

# Testar
Confirme zeros.

# Quebrar propositalmente
```bash
bq query --use_legacy_sql=false '
INSERT INTO `'"${PROJECT_ID}"'.retail_quality.raw` VALUES("o1",999),(NULL,-1);'
```

# Troubleshooting
**Sintoma:** score falha. **Hipótese:** nulo/dup/negativo. **Evidência:** contadores e linhas. **Causa:** source inválido. **Correção:** quarantine + corrigir origem/transform.

# Corrigir
Crie trusted com `id IS NOT NULL AND amount>=0` e dedup por id.

# Serviços semelhantes e critérios de escolha
Quality SQL/Dataform assertions para lógica simples; Dataplex quality para governança/monitoramento gerenciado conforme cenário.

# Pegadinhas e decisões típicas da prova
0 erros nem sempre é threshold correto. Descartar inválido pode violar auditoria.

# Questões estilo Professional Data Engineer
1. Dado chega 4h atrasado: qual dimensão?
2. Regra crítica falha: promover trusted?
3. Inválidos devem sumir silenciosamente?

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
bq rm -r -f "${PROJECT_ID}:retail_quality"
```
