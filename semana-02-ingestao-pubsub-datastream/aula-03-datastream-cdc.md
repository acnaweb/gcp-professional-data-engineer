# Aula 3 — Datastream e CDC

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
- Entender backfill + CDC.
- Inspecionar Datastream sem criar banco pago.
- Diagnosticar pré-requisitos da origem.

# Conceito
Datastream captura mudanças log-based de bancos suportados. CDC não é polling de `SELECT *`; depende de logs, privilégios e conectividade.

## Arquitetura construída
```text
PostgreSQL/MySQL/... → transaction log → Datastream → BigQuery/GCS
```

# Criar
```bash
gcloud services enable datastream.googleapis.com
gcloud datastream connection-profiles list --location="${REGION}"
gcloud datastream streams list --location="${REGION}"
cat > cdc_design.txt <<'EOF'
source=postgresql
mode=cdc
backfill=all
destination=bigquery
private_connectivity=required_in_prod
EOF
```

# Inspecionar
```bash
gcloud datastream operations list --location="${REGION}"
cat cdc_design.txt
```

# Testar
Explique a sequência: backfill inicial → posição do log → mudanças contínuas → schema evolution → monitoring.

# Quebrar propositalmente
```bash
cp cdc_design.txt cdc_design.good
sed -i 's/mode=cdc/mode=full_reload_every_minute/' cdc_design.txt
```

# Troubleshooting
**Sintoma:** carga excessiva/full scans/duplicações. **Hipótese:** padrão não é CDC. **Evidência:** design usa full reload. **Causa:** ingestão incompatível com requisito incremental. **Correção:** log-based CDC. Em falhas reais inspecione WAL/binlog, replication privileges, portas, private connectivity e schema.

# Corrigir
```bash
mv cdc_design.good cdc_design.txt
```

# Serviços semelhantes e critérios de escolha
Pub/Sub = eventos de aplicação; Datastream = CDC; Storage Transfer = object transfer; Transfer Appliance = offline massivo.

# Pegadinhas e decisões típicas da prova
CDC tem backfill e fase contínua. “Serverless” não elimina pré-requisitos na origem.

# Questões estilo Professional Data Engineer
1. Updates/inserts PostgreSQL para analytics sem full reload: qual serviço?
2. Conexão funciona mas CDC não inicia: o que checar?
3. Eventos já nascem na app: Datastream ou Pub/Sub?

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
rm -f cdc_design.txt
```
