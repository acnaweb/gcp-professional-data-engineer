# Aula 2 — BigQuery Performance e Custo

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
- Usar INFORMATION_SCHEMA.JOBS.
- Diagnosticar scan e slot_ms.

# Conceito
Performance envolve scan, shuffle, skew, joins, materializações e capacidade. Custo on-demand depende de dados processados; capacity models atendem previsibilidade/concorrência.

## Arquitetura construída
```text
query → scan → shuffle → aggregate → result
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_perf" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_perf.t`
PARTITION BY d AS
SELECT DATE_ADD(DATE "2026-01-01",INTERVAL MOD(x,30) DAY) d,x id,REPEAT("x",100) payload,RAND()*10 amount
FROM UNNEST(GENERATE_ARRAY(1,200000)) x;'
```

# Inspecionar
```bash
bq query --use_legacy_sql=false '
SELECT creation_time,job_id,total_bytes_processed,total_slot_ms
FROM `region-us`.INFORMATION_SCHEMA.JOBS_BY_PROJECT
WHERE project_id="'"${PROJECT_ID}"'" ORDER BY creation_time DESC LIMIT 10;'
```

# Testar
Compare dry run `SELECT *` sem filtro vs colunas mínimas + filtro.

# Quebrar propositalmente
Use `SELECT * FROM t LIMIT 10` e assuma que LIMIT reduzirá leitura.

# Troubleshooting
**Sintoma:** bytes continuam altos. **Hipótese:** LIMIT só limita output. **Evidência:** dry run. **Causa:** scan ainda amplo. **Correção:** pruning/colunas/modelagem.

# Corrigir
Selecione colunas e filtre `d`.

# Serviços semelhantes e critérios de escolha
On-demand=scan; reservations/editions=capacidade; materialized view/BI Engine podem ajudar workloads repetitivos.

# Pegadinhas e decisões típicas da prova
`LIMIT` não é estratégia de custo. Slot ms alto pode indicar shuffle, não só scan.

# Questões estilo Professional Data Engineer
1. Concorrência previsível alta: o que avaliar?
2. Dashboard repete agregação: o que considerar?
3. Scan alto: comprar slots é primeira correção?

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
bq rm -r -f "${PROJECT_ID}:retail_perf"
```
