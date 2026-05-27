# Imagens Clínicas — Clinix

> Estratégia de captura, armazenamento, processamento, exibição e descarte de imagens clínicas no Clinix.

---

# 1. VISÃO GERAL

Imagens clínicas são essenciais em várias especialidades:

* **dermatologia** — fotografia de lesão, dermatoscopia
* **estética** — antes/depois de procedimentos
* **odontologia** — radiografias e fotos intraorais
* **fisioterapia** — postura, ROM em foto
* **nutrição** — fotos de evolução corporal
* **biomedicina** — anexos de laudos com imagem

Esse volume é alto, sensível (LGPD) e exige UX rica (comparação, zoom, anotação). Centralizar a estratégia em um módulo evita duplicação.

---

# 2. OBJETIVOS

* upload performante (mesmo em internet ruim regional)
* armazenamento barato e escalável (Cloudflare R2)
* exibição rápida via CDN
* compressão e thumbnails automáticos
* metadados ricos (data, ângulo, condições)
* consentimento LGPD explícito por imagem
* comparação antes/depois fluida
* retenção e anonimização controladas

---

# 3. PRINCÍPIOS

* **toda imagem é sensível** — privada por padrão
* **consentimento por imagem** — não só genérico do paciente
* **metadados como cidadãos de primeira classe**
* **nunca confiar no mime-type do cliente** — validar server-side
* **CDN para tudo que pode ser público** (logos, thumbs públicos), nada além disso

---

# 4. ENTIDADES

## ClinicalImage

```python
class ClinicalImage(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    record_entry = models.ForeignKey(RecordEntry, null=True, ...)
    treatment_package_session = models.ForeignKey(
        TreatmentPackageSession, null=True, ...
    )

    # Storage
    storage_key = models.CharField(max_length=500)  # path no R2
    thumb_keys = models.JSONField()  # {200: key, 800: key, 2000: key}
    original_mime = models.CharField(max_length=50)
    output_mime = models.CharField(max_length=50)  # webp ou avif
    width = models.PositiveIntegerField()
    height = models.PositiveIntegerField()
    bytes = models.BigIntegerField()

    # Metadados clínicos
    category = models.CharField(max_length=30)  # before, after, dermatoscopy, etc
    body_region = models.CharField(max_length=100, blank=True)
    angle = models.CharField(max_length=30, blank=True)  # frontal, lateral...
    lighting = models.CharField(max_length=30, blank=True)
    notes = models.TextField(blank=True)
    captured_at = models.DateTimeField()

    # Vínculo entre antes/depois
    paired_with = models.ForeignKey('self', null=True, ...)

    # LGPD
    consent_id = models.ForeignKey(Consent, ...)
    anonymized = models.BooleanField(default=False)
    anonymized_at = models.DateTimeField(null=True)

    # Auditoria
    created_at = models.DateTimeField(auto_now_add=True)
```

---

# 5. CONVENÇÕES DE PATH NO R2

```
{tenant_id}/patients/{patient_id}/images/{image_id}/original.{ext}
{tenant_id}/patients/{patient_id}/images/{image_id}/thumb_200.webp
{tenant_id}/patients/{patient_id}/images/{image_id}/thumb_800.webp
{tenant_id}/patients/{patient_id}/images/{image_id}/thumb_2000.webp
```

Detalhes em `ADRs/007-storage-cloudflare-r2.md`.

---

# 6. PIPELINE DE UPLOAD

1. Frontend solicita **presigned PUT** ao backend (`POST /api/v1/uploads/clinical-image/sign`).
2. Backend valida permissão, gera `image_id` e retorna URL presigned + storage_key.
3. Frontend faz upload direto ao R2.
4. Frontend confirma ao backend (`POST /api/v1/clinical-images/{id}/finalize`).
5. Backend dispara task Celery `process_clinical_image(id)`:
   * baixa do R2
   * valida formato real (sniff de bytes)
   * gera thumbnails 200/800/2000
   * comprime em WebP (e AVIF se navegador suportar)
   * extrai dimensões e tamanho
   * persiste `ClinicalImage`
   * remove original se política indicar
6. Task notifica frontend via WebSocket ou event-driven.

---

# 7. COMPRESSÃO

* original PNG/JPEG/HEIC → WebP (lossless quando possível)
* thumbs em WebP e AVIF
* qualidade alvo: 85% (regulável por tenant)
* tamanho máximo do original: 25 MB
* validação de dimensões: max 8000x8000 px

---

# 8. THUMBNAILS

| Tamanho | Uso |
|---------|-----|
| 200 px | listagens, miniaturas em timeline |
| 800 px | visualização inline em prontuário |
| 2000 px | abertura ampliada / antes-depois |
| original | download protegido com URL assinada |

Gerados em paralelo no Celery via `pillow-heif` e `pillow-avif`.

---

# 9. CDN E SERVIR

* thumbs em `https://cdn.clinix.app/...` (Cloudflare)
* original sempre via URL assinada com expiração 5–15 min
* `Cache-Control: private, max-age=300` no original
* `Cache-Control: public, max-age=31536000, immutable` em thumbs (URL contém hash da imagem)

---

# 10. ANTES E DEPOIS

* duas `ClinicalImage` ligadas via `paired_with`
* componente `<BeforeAfterSlider>` no frontend
* metadados precisam casar (mesma região, ângulo, iluminação se possível)
* aviso visual quando metadados divergem (impede comparação enganosa)
* exportável em PDF com marca d'água do tenant

---

# 11. ANOTAÇÕES

Profissional pode anotar sobre a imagem (sem alterar o pixel original):

* desenhos vetoriais sobrepostos (SVG)
* texto livre por região
* persistido em `annotations_json` separado
* exibido como camada sobre a imagem

---

# 12. METADADOS CLÍNICOS

Cada imagem deve registrar:

* paciente
* profissional
* especialidade
* tipo de procedimento (se aplicável)
* região corporal
* ângulo
* condições de iluminação
* data e hora de captura
* equipamento (se possível extrair de EXIF)

EXIF é parseado mas **dados de geolocalização são removidos** antes de persistir.

---

# 13. CONSENTIMENTO LGPD

Toda imagem deve ter `consent_id` apontando para um termo digital aceito pelo paciente:

* termo genérico de uso na clínica (no cadastro)
* termo específico para fotos antes/depois (estética)
* termo específico para uso em pesquisa/portfólio (opt-in separado)
* termo registra IP, timestamp, hash e versão do termo

Sem consentimento ativo, a imagem **não pode** ser usada fora do prontuário (ex: divulgação, treinamento, marketing).

---

# 14. ACESSO

* paciente: vê apenas suas próprias imagens via portal
* profissional: vê imagens do paciente que está atendendo (RBAC + relação)
* outros profissionais: só com `break_glass` justificado e auditado
* admin Clinix: nunca acessa imagens em prod (apenas em incidente, com aprovação)

Toda visualização registra audit log (`clinical_image.viewed`).

---

# 15. RETENÇÃO

* período padrão: tempo legal mínimo (20 anos a partir do último atendimento, conforme CFM/CFO)
* paciente pode solicitar exclusão (LGPD art. 18) — admin valida obrigação legal antes
* exclusão é **anonimização**: pixel preservado, vínculo com paciente removido (para usar como histórico operacional sem expor identidade)

---

# 16. ANONIMIZAÇÃO

Quando aplicada:

* remove `patient_id` (substitui por hash anônimo)
* remove EXIF residual
* remove rosto via blur automatizado opcional (para uso futuro em pesquisa)
* registra hash + data em `anonymization_log`

---

# 17. EXPORTAÇÃO

* paciente pode exportar suas imagens (LGPD)
* clínica pode exportar todas as imagens de um paciente (offboarding)
* exportação gera ZIP assinado com manifest JSON dos metadados

---

# 18. PERFORMANCE

* upload via presigned: zero carga no backend
* compressão em Celery: não bloqueia request
* thumbs em CDN: cache hit > 95% esperado
* loading lazy nas timelines (`<img loading="lazy">`)

Detalhes de SLOs em `docs/performance.md`.

---

# 19. SEGURANÇA

* sniff de magic bytes obrigatório (não confia em mime do cliente)
* tamanho e dimensão validados antes de processar
* malware scanner opcional (ClamAV) em fase futura
* URLs assinadas com IP-pinning opcional
* `Content-Disposition` corretamente configurado para downloads

---

# 20. WHATSAPP E PORTAL

* link de imagem enviado por WhatsApp usa URL assinada curta (15 min)
* portal do paciente carrega via thumb 800 + zoom para 2000
* nunca enviar `body` da imagem inline em mensagem — apenas link

---

# 21. EXEMPLOS DE USO POR ESPECIALIDADE

* **dermatologia** — `category=dermatoscopy`, `body_region=braço direito`
* **estética** — `category=before` + `category=after` com `paired_with`
* **odontologia** — `category=xray`, `body_region=dente 21`
* **fisioterapia** — `category=postural`, ângulo lateral
* **nutrição** — `category=evolution`, vínculo com antropometria

---

# 22. O QUE EVITAR

* salvar imagem sem consentimento
* compartilhar URL não assinada
* armazenar EXIF de geolocalização
* expor imagens entre tenants
* gerar thumb sincronicamente no request
* deletar `ClinicalImage` sem audit log
* permitir upload de formato desconhecido

---

# 23. OBJETIVO FINAL

Construir uma camada de imagens clínicas:

* eficiente
* segura
* compatível com LGPD
* premium em UX
* preparada para crescer com o catálogo de especialidades

---

# 24. REFERÊNCIAS

* `ADRs/007-storage-cloudflare-r2.md`
* `docs/dermatologia.md`
* `docs/estetica.md`
* `docs/lgpd.md`
* `docs/performance.md`
* `docs/portal-paciente.md`
* `docs/prontuario.md`
* `docs/database-schema.md`
