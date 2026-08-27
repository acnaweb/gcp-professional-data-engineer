# Aula 1 — Revisão por Domínios

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
- Fazer autoavaliação baseada em evidência.

# Conceito
Prontidão exige explicar, criar/inspecionar, quebrar, corrigir e decidir. Leitura passiva gera falsa fluência.

## Arquitetura construída
```text
Design → Ingest/Process → Store → Prepare/Use → Maintain/Automate
```

# Criar
```bash
printf 'domain,score\ndesign,0\ningest_process,0\nstore,0\nprepare_use,0\nmaintain_automate,0\n' > readiness.csv
```

# Inspecionar
```bash
column -s, -t readiness.csv
```

# Testar
Preencha 0–5 com evidência prática.

# Quebrar propositalmente
Dê nota 5 a um tema que você não consegue troubleshootar.

# Troubleshooting
**Sintoma:** confiança alta, cenário baixo. **Hipótese:** falsa fluência. **Evidência:** não consegue explicar falha/decisão. **Causa:** score sem evidência. **Correção:** reexecutar lab.

# Corrigir
Nota 5 apenas se consegue explicar + operar + diagnosticar.

# Serviços semelhantes e critérios de escolha
Revise por capacidade, não por lista de produtos.

# Pegadinhas e decisões típicas da prova
A prova cruza domínios na mesma questão.

# Questões estilo Professional Data Engineer
1. Sabe definição mas não troubleshooting: pronto?
2. BigQuery sem custo/performance: lacuna?
3. IAM sem VPC-SC: lacuna?

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
rm -f readiness.csv
```
