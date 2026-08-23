# Aula 5 — Lab End-to-End: Data Platform Base

## Objetivo
Montar um fluxo simples de ingestão e analytics.

## Arquitetura
```text
CSV → Cloud Storage → BigQuery → SQL analytics
```

## Passos
1. Criar bucket.
2. Criar dataset.
3. Fazer upload de CSV.
4. Carregar tabela.
5. Criar consulta analítica.

```bash
gcloud storage buckets create gs://$GOOGLE_CLOUD_PROJECT-pde-lab --location=southamerica-east1
bq mk --dataset $GOOGLE_CLOUD_PROJECT:pde_lab
```

Exemplo de load:
```bash
bq load --autodetect --source_format=CSV   pde_lab.customers   gs://$GOOGLE_CLOUD_PROJECT-pde-lab/customers.csv
```

## Validação
```sql
SELECT COUNT(*) FROM `pde_lab.customers`;
```

## Checklist
- [ ] Bucket criado
- [ ] Dataset criado
- [ ] Dados carregados
- [ ] Consulta executada
