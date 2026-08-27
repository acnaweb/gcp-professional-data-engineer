# Aula 4 — Segurança e Networking de Dados

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
- Criar SA/bucket IAM.
- Provocar 403.
- Inspecionar VPC-SC sem Organization.

# Conceito
IAM, VPC-SC, CMEK, private networking e Secret Manager resolvem camadas diferentes.

## Arquitetura construída
```text
workload SA → IAM → data
service perimeter → anti-exfiltration boundary
```

# Criar
```bash
gcloud iam service-accounts create retail-pipeline-sa 2>/dev/null || true
export SA="retail-pipeline-sa@${PROJECT_ID}.iam.gserviceaccount.com"
export SB="${PROJECT_ID}-pde-sec"; gcloud storage buckets create "gs://${SB}" --location="${REGION}"
echo secret > s.txt; gcloud storage cp s.txt "gs://${SB}/"
gcloud access-context-manager perimeters list 2>/dev/null || true
```

# Inspecionar
```bash
gcloud storage buckets get-iam-policy "gs://${SB}"
gcloud iam service-accounts describe "${SA}"
```

# Testar
Conceda `roles/storage.objectViewer` no bucket e inspecione policy.

# Quebrar propositalmente
Remova role e tente acesso via SA/impersonation se permitido.

# Troubleshooting
**Sintoma:** 403. **Hipóteses:** IAM, impersonation, perimeter. **Evidência:** bucket IAM, SA policy, erro VPC-SC. **Causa:** identificar camada. **Correção:** menor privilégio ou perimeter policy adequada.

# Corrigir
Restaure objectViewer apenas se necessário para teste.

# Serviços semelhantes e critérios de escolha
IAM=autorização; VPC-SC=perímetro; CMEK=chave; private IP=rede; Secret Manager=segredo.

# Pegadinhas e decisões típicas da prova
Owner não corrige VPC-SC. VPC-SC depende de Organization/access policy, portanto inspeção é suficiente no lab.

# Questões estilo Professional Data Engineer
1. IAM correto mas perimeter bloqueia: onde olhar?
2. Só ler objetos: qual role?
3. Chave customer-managed: qual mecanismo?

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
gcloud storage rm -r "gs://${SB}"; gcloud iam service-accounts delete "${SA}" --quiet; rm -f s.txt
```
