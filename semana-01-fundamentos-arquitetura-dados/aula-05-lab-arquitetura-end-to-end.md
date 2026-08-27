# Aula 5 — Lab End-to-End: Data Platform Base

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
- Integrar landing/raw/trusted.
- Introduzir dado inválido.
- Criar quality gate e corrigir.

# Conceito
Uma camada trusted deve representar qualidade/contrato, não apenas uma cópia da raw. Registros inválidos devem ser observáveis e, frequentemente, quarentenados.

## Arquitetura construída
```text
GCS landing → BQ raw → quality → BQ trusted
                         └→ quarantine
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_trusted" 2>/dev/null || true
cat > lab_orders.csv <<'EOF'
order_id,customer_id,order_ts,amount,status
o10,c10,2026-08-26T12:00:00Z,200,PAID
o11,c11,2026-08-26T12:10:00Z,80,PAID
EOF
gcloud storage cp lab_orders.csv "gs://${BUCKET}/landing/lab_orders.csv"
bq load --autodetect --source_format=CSV --skip_leading_rows=1  "${PROJECT_ID}:retail_raw.lab_orders" "gs://${BUCKET}/landing/lab_orders.csv"
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_trusted.orders` AS
SELECT order_id,customer_id,TIMESTAMP(order_ts) order_ts,CAST(amount AS NUMERIC) amount,status
FROM `'"${PROJECT_ID}"'.retail_raw.lab_orders` WHERE amount>=0;'
```

# Inspecionar
```bash
gcloud storage ls "gs://${BUCKET}/landing/"
bq show --schema "${PROJECT_ID}:retail_trusted.orders"
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT COUNT(*) rows,SUM(amount) revenue FROM `'"${PROJECT_ID}"'.retail_trusted.orders`'
```

# Quebrar propositalmente
```bash
bq query --use_legacy_sql=false '
INSERT INTO `'"${PROJECT_ID}"'.retail_trusted.orders`
VALUES("bad","cX",CURRENT_TIMESTAMP(),-999,"PAID");'
```

# Troubleshooting
**Sintoma:** receita/qualidade incoerente.  
**Hipótese:** dado inválido chegou à trusted.  
**Evidência:** `WHERE amount<0`.  
**Causa:** quality gate não protegeu inserção direta.  
**Correção:** quarantine/assertion e controle do caminho de escrita.

# Corrigir
```bash
bq query --use_legacy_sql=false 'DELETE FROM `'"${PROJECT_ID}"'.retail_trusted.orders` WHERE amount<0'
bq query --use_legacy_sql=false 'SELECT COUNT(*) invalid FROM `'"${PROJECT_ID}"'.retail_trusted.orders` WHERE amount<0 OR order_id IS NULL'
```

# Serviços semelhantes e critérios de escolha
- SQL BigQuery: ELT simples.
- Dataform: SQL versionado, dependências/assertions.
- Dataflow: transformação complexa/stream.
- Dataproc: Spark/Hadoop.

# Pegadinhas e decisões típicas da prova
- Não descarte inválidos silenciosamente se auditoria é requisito.
- Quality gate precisa de observabilidade.
- Reprocessamento deve ser idempotente.

# Questões estilo Professional Data Engineer
1. SQL DAG com testes: qual evolução natural?
2. Inválidos precisam auditoria: descartar ou quarantine?
3. Event time e janelas: SQL batch ou Dataflow?

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
bq rm -r -f "${PROJECT_ID}:retail_trusted"
bq rm -f -t "${PROJECT_ID}:retail_raw.lab_orders" || true
gcloud storage rm "gs://${BUCKET}/landing/lab_orders.csv" || true
rm -f lab_orders.csv
```
