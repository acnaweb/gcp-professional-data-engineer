# Aula 5 — Lab: Dataflow Template

## Objetivo
Executar um pipeline gerenciado usando template.

## Fluxo
```text
Cloud Storage → Dataflow → BigQuery
```

## Passos
1. Criar bucket staging.
2. Criar tabela destino.
3. Executar template Google-provided adequado.
4. Acompanhar job e métricas.

Exemplo de listagem:
```bash
gcloud dataflow jobs list --region=southamerica-east1
```

## Checklist
- [ ] Job iniciado
- [ ] Workers observados
- [ ] Métricas verificadas
