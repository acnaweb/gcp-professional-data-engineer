# Aula 2 — Dataflow Streaming

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
- Diagnosticar backlog, freshness e hot keys.

# Conceito
Streaming Dataflow deve ser operado por métricas de progresso: backlog, data freshness, system lag, worker logs, quota e bottlenecks.

## Arquitetura construída
```text
Pub/Sub → Dataflow(group key) → BigQuery
```

# Criar
```bash
python - <<'PY'
from collections import Counter
d=['HOT']*10000+[f'k{i}' for i in range(1000)]
print(Counter(d).most_common(5))
PY
```

# Inspecionar
Em job real: `gcloud dataflow jobs list --region="${REGION}"` e Cloud Monitoring/Logging.

# Testar
Compare distribuição HOT com 100 salts:
```bash
python - <<'PY'
from collections import Counter
print(Counter([f'HOT#{i%100}' for i in range(10000)]).most_common(3))
PY
```

# Quebrar propositalmente
Considere solução “adicionar workers” sem tratar a hot key.

# Troubleshooting
**Sintoma:** backlog/freshness sobem, CPUs desiguais. **Hipótese:** hot key. **Evidência:** key distribution/bottleneck logs. **Causa:** skew. **Correção:** rekey/salt/fanout; verificar também sink/quota.

# Corrigir
Use salting/fanout e re-agregação lógica.

# Serviços semelhantes e critérios de escolha
Mais workers resolve capacidade geral; hot key exige paralelização lógica. 429 aponta quota/sink throttling.

# Pegadinhas e decisões típicas da prova
Autoscaling não corrige chave única. Backlog e freshness juntas ajudam a separar causas.

# Questões estilo Professional Data Engineer
1. HOT key domina 80%: aumentar workers basta?
2. 429 no BigQuery sink: investigar o quê?
3. Backlog e freshness crescem: qual sinal?

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
Nenhum recurso cloud criado.
