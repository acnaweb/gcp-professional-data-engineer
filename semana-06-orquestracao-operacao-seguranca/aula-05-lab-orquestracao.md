# Aula 5 — Lab Integrado de Orquestração

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
- Criar quality gate em Workflow.
- Executar success/failure.

# Conceito
Orquestração deve distinguir falha transitória de falha determinística de qualidade.

## Arquitetura construída
```text
ingest → quality ─fail→ stop
              └pass→ publish
```

# Criar
```bash
cat > gate.yaml <<'EOF'
main:
  params: [args]
  steps:
    - gate:
        switch:
          - condition: ${default(map.get(args,"errors"),0) > 0}
            next: fail
        next: ok
    - ok:
        return: "PUBLISHED"
    - fail:
        raise: "QUALITY_GATE_FAILED"
EOF
gcloud workflows deploy retail-gate --location="${REGION}" --source=gate.yaml
```

# Inspecionar
```bash
gcloud workflows describe retail-gate --location="${REGION}"
```

# Testar
```bash
gcloud workflows run retail-gate --location="${REGION}" --data='{"errors":0}'
```

# Quebrar propositalmente
```bash
gcloud workflows run retail-gate --location="${REGION}" --data='{"errors":3}'
```

# Troubleshooting
**Sintoma:** FAILED. **Hipótese:** quality errors. **Evidência:** input/execution. **Causa:** gate funcionando. **Correção:** corrigir/quarentenar dado; não retry infinito.

# Corrigir
Reexecute com `errors=0`.

# Serviços semelhantes e critérios de escolha
Retry é para transitório; quality determinística pede correção/quarantine.

# Pegadinhas e decisões típicas da prova
FAILED pode ser comportamento correto de negócio. Retry 10x não limpa dado inválido.

# Questões estilo Professional Data Engineer
1. Quality fail determinística: retry infinito?
2. Fluxo API pequeno: Workflows ou Composer?
3. SQL subgraph: onde manter?

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
gcloud workflows delete retail-gate --location="${REGION}" --quiet; rm -f gate.yaml
```
