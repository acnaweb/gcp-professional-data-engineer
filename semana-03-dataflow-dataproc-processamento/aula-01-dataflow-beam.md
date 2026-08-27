# Aula 1 — Dataflow e Apache Beam

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
- Executar Beam local.
- Tratar bad records.
- Entender Beam runner x Dataflow.

# Conceito
Beam define pipeline; Dataflow é runner gerenciado com workers, autoscaling, monitoring e integração GCP.

## Arquitetura construída
```text
input → PCollection → transforms → output
```

# Criar
```bash
python3 -m venv ~/beam3; source ~/beam3/bin/activate; pip install apache-beam >/dev/null
cat > beam_batch.py <<'PY'
import apache_beam as beam
with beam.Pipeline() as p:
 (p|beam.Create(["c1,100","c1,50","c2,80"])
   |beam.Map(lambda s:s.split(","))
   |beam.Map(lambda x:(x[0],float(x[1])))
   |beam.CombinePerKey(sum)|beam.Map(print))
PY
python beam_batch.py
```

# Inspecionar
```bash
python -m py_compile beam_batch.py
```

# Testar
Confirme saída `c1=150` e `c2=80`.

# Quebrar propositalmente
Adicione `"c3,BAD"` ao `Create` e execute.

# Troubleshooting
**Sintoma:** ValueError no float. **Hipótese:** dado malformado. **Evidência:** stack trace. **Causa:** parser sem tratamento. **Correção:** validação + bad-record output/quarantine.

# Corrigir
Filtre/parseie com função segura e reexecute.

# Serviços semelhantes e critérios de escolha
Dataflow=Beam; Dataproc=Spark/Hadoop; BigQuery/Dataform=SQL ELT.

# Pegadinhas e decisões típicas da prova
Erro de um elemento pode causar retry de bundle. Poison data precisa tratamento, não apenas mais workers.

# Questões estilo Professional Data Engineer
1. Beam batch/stream: qual runner gerenciado?
2. Spark legado: qual serviço?
3. Bad record recorrente: retry infinito?

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
rm -f beam_batch.py; deactivate 2>/dev/null || true
```
