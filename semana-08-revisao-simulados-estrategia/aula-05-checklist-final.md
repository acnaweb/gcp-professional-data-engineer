# Aula 5 — Checklist Final

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
- Fazer readiness operacional e cleanup.

# Conceito
Conhecer serviço = saber escolher, inspecionar, diagnosticar e operar — não só defini-lo.

## Arquitetura construída
```text
explain + build/inspect + break + troubleshoot + choose
```

# Criar
```bash
printf '%s\n' PubSub Dataflow Dataproc Datastream BigQuery Dataform Dataplex Composer Workflows Security Quality Observability > final.txt
```

# Inspecionar
```bash
gcloud pubsub topics list
gcloud dataflow jobs list --region="${REGION}"
gcloud dataproc clusters list --region="${REGION}"
gcloud workflows list --location="${REGION}"
gcloud storage buckets list
bq ls
```

# Testar
Para cada item de final.txt, responda: quando usar, quando não usar, falha típica, evidência, correção.

# Quebrar propositalmente
Marque como pronto um tema em que só sabe definição.

# Troubleshooting
**Sintoma:** acerta perguntas diretas e erra cenários. **Causa:** estudo conceitual sem operação. **Correção:** repetir lab do domínio fraco.

# Corrigir
Critério: ≥80% + labs + erros compreendidos + decisões justificadas.

# Serviços semelhantes e critérios de escolha
Para recursos caros, inspeção/troubleshooting consistente pode substituir provisionamento artificial no estudo.

# Pegadinhas e decisões típicas da prova
Não deixe recursos de lab. Custo e quotas fazem parte da competência Professional.

# Questões estilo Professional Data Engineer
1. Nunca criou Composer por custo mas sabe DAG/logs/retries: aceitável?
2. Criou Dataflow mas não entende backlog: pronto?
3. 85% geral e segurança fraca: próxima ação?

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
rm -f final.txt
# Remova manualmente apenas recursos de laboratório ainda listados pelos comandos acima.
```
