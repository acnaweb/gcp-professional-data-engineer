# Aula 1 — BigQuery ML

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
- Treinar/evaluar modelo simples.
- Quebrar label.

# Conceito
BigQuery ML leva treinamento/prediction para SQL sobre dados no warehouse. Data leakage e qualidade de features continuam críticos.

## Arquitetura construída
```text
BQ table → CREATE MODEL → ML.EVALUATE → ML.PREDICT
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_ml" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_ml.train` AS
SELECT 1 label,10 visits,100 amount UNION ALL SELECT 0,1,10 UNION ALL SELECT 1,8,80 UNION ALL SELECT 0,2,20;
CREATE OR REPLACE MODEL `'"${PROJECT_ID}"'.retail_ml.model`
OPTIONS(model_type="logistic_reg",input_label_cols=["label"]) AS
SELECT * FROM `'"${PROJECT_ID}"'.retail_ml.train`;'
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_ml.model" | head -50
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM ML.EVALUATE(MODEL `'"${PROJECT_ID}"'.retail_ml.model`,(SELECT * FROM `'"${PROJECT_ID}"'.retail_ml.train`))'
```

# Quebrar propositalmente
Crie modelo com `input_label_cols=["target"]` inexistente.

# Troubleshooting
**Sintoma:** label não encontrada. **Hipótese:** schema/config mismatch. **Evidência:** training schema. **Causa:** target errado. **Correção:** usar `label`. Em produção, também investigue leakage/imbalance.

# Corrigir
Use modelo válido.

# Serviços semelhantes e critérios de escolha
BQML=SQL-first; Vertex AI=training custom/serving lifecycle mais amplo.

# Pegadinhas e decisões típicas da prova
99% accuracy com feature futura pode ser leakage. PDE prepara dados e garante freshness.

# Questões estilo Professional Data Engineer
1. Dados no BQ + modelo suportado + equipe SQL: qual?
2. GPU custom training: qual?
3. Feature futura no treino: problema?

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
bq rm -r -f "${PROJECT_ID}:retail_ml"
```
