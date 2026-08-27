# Aula 3 — Segurança, Governança e Compliance

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
- Aplicar least privilege.
- Criar view segura.
- Diagnosticar Access Denied.
- Comparar row/column security e VPC-SC.

# Conceito
Governança inclui IAM, classificação, lineage, auditoria, qualidade e controles de acesso. Para BigQuery, escolha o mecanismo mais específico: row-level, column-level/policy tags, masking, authorized views e perimeter controls.

## Arquitetura construída
```text
private table → controlled view/row/column policy → consumer
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_sec" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_sec.customers` AS
SELECT "c1" id,"Alice" name,"111" tax_id UNION ALL SELECT "c2","Bob","222";'
bq query --use_legacy_sql=false '
CREATE OR REPLACE VIEW `'"${PROJECT_ID}"'.retail_sec.customers_safe` AS
SELECT id,name FROM `'"${PROJECT_ID}"'.retail_sec.customers`;'
gcloud iam service-accounts create pde-reader 2>/dev/null || true
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_sec.customers_safe"
gcloud iam service-accounts describe "pde-reader@${PROJECT_ID}.iam.gserviceaccount.com"
gcloud projects get-iam-policy "${PROJECT_ID}" --format="table(bindings.role)"
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_sec.customers_safe`'
```

# Quebrar propositalmente
Se você puder impersonar a SA, tente sem dataViewer:
```bash
bq --impersonate_service_account="pde-reader@${PROJECT_ID}.iam.gserviceaccount.com"  query --use_legacy_sql=false  'SELECT * FROM `'"${PROJECT_ID}"'.retail_sec.customers` LIMIT 1'
```

# Troubleshooting
**Sintoma:** Access Denied.  
**Hipóteses:** SA não tem `tables.getData` ou executor não pode impersonar.  
**Evidência:** erro exato + IAM do dataset/projeto/SA.  
**Causa:** role ausente no escopo correto.  
**Correção:** conceder apenas a role necessária ao recurso apropriado.

# Corrigir
Para o lab, valide com sua identidade. Em produção, configure authorized view/row/column controls e o acesso consumidor mínimo.

# Serviços semelhantes e critérios de escolha
- Row-level: linhas.
- Column-level/policy tags: colunas.
- Masking: valor sensível.
- Authorized view: subset lógico.
- VPC-SC: perímetro contra exfiltração; complementa IAM.

# Pegadinhas e decisões típicas da prova
- Owner/Editor quase nunca é a resposta “mais segura”.
- VPC-SC não substitui IAM.
- Authorized view não significa tornar dados públicos.

# Questões estilo Professional Data Engineer
1. Usuário vê só sua região: qual controle?
2. CPF acessível só a grupo restrito: qual controle?
3. Parceiro precisa de subset sem tabela base: qual padrão?

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
gcloud iam service-accounts delete "pde-reader@${PROJECT_ID}.iam.gserviceaccount.com" --quiet || true
bq rm -r -f "${PROJECT_ID}:retail_sec"
```
