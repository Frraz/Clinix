# ADR 007 — Storage com Cloudflare R2

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix manipula um volume alto e crescente de arquivos sensíveis:

* fotos clínicas (antes/depois em estética e dermatologia)
* imagens dermatoscópicas
* dermograma e mapa corporal de lesões
* anexos de prontuário (raio-x odontológico, exames, fotos)
* laudos em PDF assinados digitalmente
* receitas, atestados e contratos
* logo e branding por tenant
* arquivos de import/export operacional (LGPD)
* backups de mídia

Esse storage precisa ser:

* compatível com S3 (para reusar `django-storages`)
* barato para egress (laudos baixados várias vezes, fotos servidas em UI)
* confiável e durável (dados médicos)
* compatível com retenção LGPD (anonimização, exclusão controlada)
* preparado para volume crescente

---

# Decisão

Adotar **Cloudflare R2** como storage primário do Clinix:

* todos os arquivos do produto vão para R2
* acesso via `django-storages` com backend S3-compatible
* paths organizados por `tenant_id/<entidade>/<id>/...`
* URLs assinadas com expiração curta para downloads sensíveis
* CDN do Cloudflare na frente para servir thumbs e estáticos
* backups de banco e mídia em bucket separado com versionamento

---

# Justificativa

## Custo

* R2 cobra apenas storage e operations
* **egress gratuito** — diferencial crítico para um SaaS que serve muitas imagens e PDFs
* preço de storage competitivo com S3, MUITO inferior ao egress AWS

## Compatibilidade

* API totalmente S3-compatible — `django-storages` funciona sem fork
* zero vendor lock-in: migrar para S3/MinIO no futuro é trivial

## Performance regional

* edge da Cloudflare cobre o Brasil bem
* baixa latência para clínicas regionais (Norte/Nordeste)

## Integração

* mesma conta da Cloudflare que serve CDN, DNS, SSL e WAF
* simplifica operação e billing

---

# Implementação

## Buckets

```
clinix-prod-files      → arquivos operacionais (fotos, anexos, PDFs)
clinix-prod-backups    → dumps de banco e mídia (versionado)
clinix-prod-public     → estáticos públicos (logos, assets do site)
```

---

## Convenções de path

```
{tenant_id}/patients/{patient_id}/photos/{photo_id}.webp
{tenant_id}/patients/{patient_id}/records/{record_id}/{filename}
{tenant_id}/prescriptions/{prescription_id}.pdf
{tenant_id}/laudos/{exam_id}.pdf
{tenant_id}/branding/logo.{ext}
```

Detalhamento em `docs/imagens-clinicas.md`.

---

## Acesso

* arquivos privados: URL assinada via backend, expiração 5–15 min
* arquivos públicos: servidos via CDN com `Cache-Control` longo
* upload direto via presigned PUT para alívio do backend

---

# Consequências

## Positivas

* egress gratuito reduz custo unitário do SaaS
* mesma stack Cloudflare para CDN/DNS/SSL/WAF/Storage
* compatibilidade S3 mantém portabilidade
* CDN próxima dos clientes regionais

---

## Negativas

* dependência adicional de Cloudflare (já temos para DNS/CDN)
* algumas features exclusivas do S3 (Glacier, Athena) não estão disponíveis no R2 — irrelevantes para o MVP
* operações no R2 ainda são cobradas (precisamos minimizar listings excessivos)

---

## Mitigações

* exportação periódica para outro provedor em caso de migração futura
* lifecycle policies para mídia antiga
* monitoramento de custo de operations

---

# Alternativas consideradas

## AWS S3

Descartado para MVP: egress caro para volume esperado de fotos clínicas e laudos. R2 entrega o mesmo modelo S3 com custo total menor.

---

## Backblaze B2

Descartado: bom custo, mas integração com CDN exige mais setup. Cloudflare unifica tudo em uma conta.

---

## MinIO self-hosted

Descartado para MVP: aumenta carga operacional do time. Reavaliar quando volume justificar (provavelmente nunca, para o ticket-alvo).

---

# Referências

* `docs/imagens-clinicas.md`
* `docs/performance.md`
* `docs/backup-dr.md`
* `docs/stack.md`
