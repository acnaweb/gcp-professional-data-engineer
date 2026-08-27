# Aula 3 — BigQuery Security e Sharing

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
- Criar view segura.
- Comparar sharing, row e column controls.

# Conceito
Compartilhamento governado não precisa copiar dados. Authorized views, data sharing, row/column controls e masking atendem requisitos distintos.

## Arquitetura construída
```text
private data → governed share/view → consumer
```

# Criar
```bash
bq mk --dataset --location=US "${PROJECT_ID}:retail_share" 2>/dev/null || true
bq query --use_legacy_sql=false '
CREATE OR REPLACE TABLE `'"${PROJECT_ID}"'.retail_share.private` AS SELECT "c1" id,"Alice" name,"111" tax_id;
CREATE OR REPLACE VIEW `'"${PROJECT_ID}"'.retail_share.safe` AS SELECT id,name FROM `'"${PROJECT_ID}"'.retail_share.private`;'
```

# Inspecionar
```bash
bq show --format=prettyjson "${PROJECT_ID}:retail_share.safe"
```

# Testar
```bash
bq query --use_legacy_sql=false 'SELECT * FROM `'"${PROJECT_ID}"'.retail_share.safe`'
```

# Quebrar propositalmente
Tente acessar base com identidade sem dataViewer (impersonation opcional).

# Troubleshooting
**Sintoma:** Access Denied. **Hipótese:** consumer não tem base access. **Evidência:** IAM. **Causa:** least privilege. **Correção:** expor apenas mecanismo governado necessário.

# Corrigir
Configure authorized view/data sharing no cenário real; não conceda Owner.

# Serviços semelhantes e critérios de escolha
Authorized view=subset SQL; Analytics Hub/data sharing=data product; row-level=linhas; column-level=colunas; masking=valor.

# Pegadinhas e decisões típicas da prova
Sharing não é sinônimo de copiar tabela. Row-level não protege coluna por si só.

# Questões estilo Professional Data Engineer
1. No-copy governed share: qual família de recurso?
2. PII por coluna: qual controle?
3. Region-based rows: qual controle?

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
bq rm -r -f "${PROJECT_ID}:retail_share"
```
