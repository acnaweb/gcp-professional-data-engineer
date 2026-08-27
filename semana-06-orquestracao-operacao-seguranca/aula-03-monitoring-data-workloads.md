# Aula 3 — Monitoring de Data Workloads

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
- Escrever logs.
- Criar log-based metric.
- Provocar falso positivo.

# Conceito
Operação de dados usa backlog, freshness, latency, throughput, quality, errors e cost. Logs trazem detalhe; metrics mostram comportamento agregado.

## Arquitetura construída
```text
pipeline → metrics/logs → alert → incident → evidence
```

# Criar
```bash
gcloud logging write retail-pipeline '{"status":"ERROR","reason":"quality"}' --payload-type=json --severity=ERROR
gcloud logging metrics create retail_errors --log-filter='logName:"retail-pipeline" AND severity=ERROR'
```

# Inspecionar
```bash
gcloud logging read 'logName:"retail-pipeline"' --limit=5 --format=json
gcloud logging metrics describe retail_errors
```

# Testar
Gere outro ERROR e confirme leitura.

# Quebrar propositalmente
Escreva `status=SUCCESS` com severity ERROR: a métrica ampla contará falso positivo.

# Troubleshooting
**Sintoma:** alerta em sucesso. **Hipótese:** filtro amplo. **Evidência:** log tem SUCCESS + ERROR severity. **Causa:** filtro só severity. **Correção:** incluir `jsonPayload.status="ERROR"`.

# Corrigir
Recrie métrica com filtro mais específico.

# Serviços semelhantes e critérios de escolha
Monitoring=trends/alerts; Logging=detail; freshness é métrica de dados; backlog mede acúmulo.

# Pegadinhas e decisões típicas da prova
CPU normal não prova pipeline saudável. Alert fatigue é falha operacional.

# Questões estilo Professional Data Engineer
1. Stack trace: logs ou metrics?
2. Dados atrasados: qual métrica?
3. Backlog cresce: qual investigação?

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
gcloud logging metrics delete retail_errors --quiet
```
