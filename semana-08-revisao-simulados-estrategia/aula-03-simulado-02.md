# Aula 3 — Simulado 2: Troubleshooting

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
- Resolver incidentes por evidência.

# Conceito
A correção causal supera brute force. Mais CPU/Owner/retry podem mascarar a causa.

## Arquitetura construída
```text
symptom → hypothesis → evidence → cause → fix
```

# Criar
```bash
printf '# Incident Notes\n' > incidents.md
```

# Inspecionar
```bash
cat incidents.md
```

# Testar
Resolva: backlog+freshness; hot key; BQ sink 429; full scan; Dataform ref missing; DAG cycle; Workflow var missing; view após rename; SA 403; perimeter; external parse; CDC não inicia; reverse ETL stale; leakage; retry duplicates.

# Quebrar propositalmente
Para hot key, escolha “mais workers” propositalmente.

# Troubleshooting
**Sintoma:** skew continua. **Hipótese:** hot key. **Evidência:** key distribution. **Causa:** serialização por key. **Correção:** fanout/rekey, não só workers.

# Corrigir
Escreva resposta causal para os 15 incidentes.

# Serviços semelhantes e critérios de escolha
Use logs/metrics/IAM/schema antes de mudar capacidade.

# Pegadinhas e decisões típicas da prova
Owner não corrige VPC-SC; retry não corrige poison data; full reload não é CDC.

# Questões estilo Professional Data Engineer
1. 12/15 sem evidência é suficiente?
2. Primeiro aumentar workers ou localizar bottleneck?
3. 403 sempre IAM?

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
rm -f incidents.md
```
