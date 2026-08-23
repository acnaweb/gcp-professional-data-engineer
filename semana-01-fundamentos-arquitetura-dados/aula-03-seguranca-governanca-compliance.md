# Aula 3 — Segurança, Governança e Compliance

## Objetivos
- Aplicar least privilege.
- Entender IAM, row/column security e proteção de dados.
- Relacionar governança com requisitos regulatórios.

## Camadas
```text
Identity → IAM
Data access → Dataset/Table/Row/Column
Sensitive data → DLP / policy tags
Network → VPC-SC / private access where applicable
Audit → Cloud Audit Logs
Governance → Dataplex / catalogs / metadata
```

## BigQuery
Estude:
- Dataset/table IAM
- Row-level security
- Column-level security
- Policy tags
- Authorized views
- CMEK quando exigido

## Laboratório
Crie uma view autorizada conceitualmente para expor apenas colunas não sensíveis.

## Regra de prova
Se a necessidade é restringir acesso por linha, prefira **row-level security**.  
Se é por coluna sensível, use **column-level security/policy tags**.

## Checklist
- [ ] Sei diferenciar controle por linha e coluna
- [ ] Entendo IAM e audit logs
- [ ] Entendo governança como requisito arquitetural
