# Aula 1 — Dataplex e Governança

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
- Inspecionar Dataplex sem criar estrutura cara.
- Modelar metadata/ownership.

# Conceito
Dataplex apoia catalogação, metadata, quality e lineage. Governança inclui ownership, classificação, descoberta e políticas.

## Arquitetura construída
```text
assets → catalog/metadata/quality/lineage → governed consumers
```

# Criar
```bash
gcloud services enable dataplex.googleapis.com
gcloud dataplex lakes list --location="${REGION}"
cat > product.yaml <<'EOF'
name: retail_orders
owner: data-platform
classification: confidential
freshness_slo_minutes: 60
EOF
```

# Inspecionar
```bash
gcloud dataplex operations list --location="${REGION}" 2>/dev/null || true
cat product.yaml
```

# Testar
Valide presença de owner com `grep '^owner:' product.yaml`.

# Quebrar propositalmente
```bash
cp product.yaml product.good; sed -i '/owner:/d' product.yaml
```

# Troubleshooting
**Sintoma:** ativo sem responsável. **Hipótese:** metadata incompleta. **Evidência:** owner ausente. **Causa:** governance contract incompleto. **Correção:** metadata obrigatória e validação automatizada.

# Corrigir
```bash
mv product.good product.yaml
```

# Serviços semelhantes e critérios de escolha
Dataplex=governança/catalog; Dataform=SQL transformation; IAM=authorization; policy tags=column access.

# Pegadinhas e decisões típicas da prova
Catalog não substitui IAM nem quality. Metadata sem processo de atualização envelhece.

# Questões estilo Professional Data Engineer
1. Descobrir owner/classificação: qual serviço?
2. Bloquear PII: catálogo basta?
3. Impacto downstream: qual conceito?

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
rm -f product.yaml
```
