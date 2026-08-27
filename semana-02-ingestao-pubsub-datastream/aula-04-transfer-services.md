# Aula 4 — Transferência de Dados

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
- Copiar dados entre buckets.
- Comparar CLI, Storage Transfer e Transfer Appliance.
- Diagnosticar destino inexistente.

# Conceito
Escolha transferência por volume, janela, largura de banda, recorrência, egress e downtime.

## Arquitetura construída
```text
source object store → transfer → GCS
```

# Criar
```bash
export SRC="${PROJECT_ID}-pde-src"; export DST="${PROJECT_ID}-pde-dst"
gcloud storage buckets create "gs://${SRC}" --location="${REGION}"
gcloud storage buckets create "gs://${DST}" --location="${REGION}"
echo retail > sample.txt
gcloud storage cp sample.txt "gs://${SRC}/"
gcloud storage cp -r "gs://${SRC}/*" "gs://${DST}/"
```

# Inspecionar
```bash
gcloud storage buckets describe "gs://${SRC}"
gcloud storage ls -r "gs://${DST}/**"
```

# Testar
```bash
gcloud storage cat "gs://${DST}/sample.txt"
```

# Quebrar propositalmente
```bash
gcloud storage cp sample.txt "gs://${PROJECT_ID}-nao-existe/sample.txt"
```

# Troubleshooting
**Sintoma:** 404 bucket. **Hipótese:** URI/destino. **Evidência:** `gcloud storage buckets list`. **Causa:** bucket não existe. **Correção:** criar/corrigir destino. Em Storage Transfer real cheque credentials, schedule, overwrite e source access.

# Corrigir
```bash
gcloud storage cp sample.txt "gs://${DST}/fixed/sample.txt"
```

# Serviços semelhantes e critérios de escolha
CLI = pequeno/manual; Storage Transfer Service = recorrente/online; Transfer Appliance = volume enorme com rede insuficiente; Datastream = CDC.

# Pegadinhas e decisões típicas da prova
Egress pode dominar custo. Não trate `cp` manual como arquitetura de migração recorrente grande.

# Questões estilo Professional Data Engineer
1. 5 PB e link lento: qual opção?
2. Cópia diária de object store externo: qual serviço?
3. Arquivo único de 10 MB: precisa appliance?

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
gcloud storage rm -r "gs://${SRC}" "gs://${DST}"
rm -f sample.txt
```
