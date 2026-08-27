# Aula 2 — Padrões de Streaming

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
- Praticar event time, fixed windows e late data com Beam local.

# Conceito
Em streaming, event time pode diferir de processing time. Windows, watermark, triggers e allowed lateness definem como eventos fora de ordem participam de agregações.

## Arquitetura construída
```text
events(event_time) → windows → aggregation → late-data policy
```

# Criar
```bash
python3 -m venv ~/pde-beam
source ~/pde-beam/bin/activate
pip install apache-beam >/dev/null
cat > window_lab.py <<'PY'
import apache_beam as beam
events=[("c1",1,0),("c1",1,20),("c1",1,70)]
with beam.Pipeline() as p:
 (p | beam.Create(events)
    | beam.Map(lambda x: beam.window.TimestampedValue((x[0],x[1]),x[2]))
    | beam.WindowInto(beam.window.FixedWindows(60))
    | beam.CombinePerKey(sum)
    | beam.Map(print))
PY
python window_lab.py
```

# Inspecionar
```bash
grep -n 'TimestampedValue\|FixedWindows' window_lab.py
```

# Testar
Troque timestamp 70 por 59 e compare a agregação:
```bash
sed -i 's/,70)/,59)/' window_lab.py
python window_lab.py
```

# Quebrar propositalmente
Faça backup e remova o `TimestampedValue`, usando timestamp implícito. Isso quebra a semântica de event time para o caso.

# Troubleshooting
**Sintoma:** eventos entram em janelas inesperadas. **Hipótese:** pipeline usa tempo errado. **Evidência:** código não extrai event timestamp. **Causa:** processing/runner time. **Correção:** timestamp do evento + watermark/late-data policy no streaming real.

# Corrigir
Restaure o arquivo e execute novamente.

# Serviços semelhantes e critérios de escolha
Fixed = intervalos fixos; sliding = janelas sobrepostas; session = inatividade. Dataflow/Beam ganha relevância quando essas semânticas são requisito.

# Pegadinhas e decisões típicas da prova
“Real-time” não significa processing time. Allowed lateness maior pode aumentar estado/custo.

# Questões estilo Professional Data Engineer
1. Evento ocorreu 10:01 e chegou 10:05: qual tempo para faturamento?
2. Sessão encerra após inatividade: qual window?
3. Late data exige quais conceitos?

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
rm -f window_lab.py
deactivate 2>/dev/null || true
```
