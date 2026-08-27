# Aula 2 — Simulado 1

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
- Fazer 20 questões arquiteturais.

# Conceito
Use requisito → pattern → escala/latência → segurança → operação/custo → serviço.

## Arquitetura construída
```text
20 questões → resposta + justificativa → error log
```

# Criar
```bash
seq 1 20 | sed 's/$/: /' > answers1.txt
```

# Inspecionar
```bash
cat answers1.txt
```

# Testar
Questões: 1 CDC PostgreSQL? 2 event-time windows? 3 Spark legado? 4 SQL DAG? 5 warehouse SQL? 6 metadata/lineage? 7 PII column? 8 service perimeter? 9 hot key? 10 full scan? 11 Airflow? 12 API orchestration? 13 wide-column? 14 distributed SQL? 15 sink 429? 16 retry duplicate? 17 stale feature? 18 offline PB transfer? 19 governed no-copy share? 20 poison records?

# Quebrar propositalmente
Responda Dataflow na questão Spark legado.

# Troubleshooting
**Sintoma:** alternativa tecnicamente capaz mas trade-off ruim. **Causa:** ignorou compatibilidade/rewrite. **Correção:** Dataproc. Classifique erros por conceito/leitura/operação/security.

# Corrigir
Gabarito esperado: Datastream, Dataflow, Dataproc, Dataform, BigQuery, Dataplex, column policy, VPC-SC, rekey/fanout, pruning, Composer, Workflows, Bigtable, Spanner, quota/sink, idempotência, freshness, Transfer Appliance, data sharing, quarantine.

# Serviços semelhantes e critérios de escolha
Reveja pares de serviços em todo erro.

# Pegadinhas e decisões típicas da prova
Não escolha solução mais complexa quando serviço gerenciado simples atende.

# Questões estilo Professional Data Engineer
1. 17/20 é suficiente sem justificar?
2. Erros todos de segurança: repetir simulado ou revisar domínio?
3. Alternativa funciona mas exige muita reescrita: pode ser pior?

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
rm -f answers1.txt
```
