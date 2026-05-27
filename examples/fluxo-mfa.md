# Fluxo de MFA — Clinix

> Jornada de ativação, uso e recuperação de MFA (autenticação multi-fator) no Clinix.

---

# 1. PERSONAS ENVOLVIDAS

* usuário (admin, gestor, profissional, recepcionista)
* admin do tenant (gerencia obrigatoriedade)
* admin Clinix (intervenção em caso de bloqueio total)
* sistema (django-otp, Celery, audit log)

---

# 2. PRÉ-CONDIÇÕES

* usuário autenticado com email+senha
* email válido configurado
* dispositivo com app autenticador (Google Authenticator, Authy, 1Password)

---

# 3. POLÍTICA DE OBRIGATORIEDADE

* **admin do tenant** — MFA obrigatório (não pode desativar)
* **profissional clínico** — MFA obrigatório
* **financeiro** — MFA obrigatório
* **gestor** — recomendado
* **recepcionista** — opcional (a critério da clínica)

Configuração por tenant em `Settings > Segurança`.

---

# 4. ATIVAÇÃO

## Passos

1. usuário acessa `Configurações > Segurança > MFA`
2. clica em "Ativar MFA"
3. sistema gera segredo TOTP via `django-otp`
4. exibe QR code (com URL `otpauth://totp/...`)
5. usuário escaneia com app autenticador
6. usuário digita código de 6 dígitos do app para confirmar
7. sistema valida com tolerância de ±1 step (30s)
8. ao confirmar, gera **8 backup codes** (mostrados uma única vez)
9. usuário marca "Salvei os códigos em local seguro" (obrigatório)
10. MFA ativado — sessão atual atualizada com claim `mfa=true`

---

## Resultado

* `User.totp_enabled = True`
* device persistido em `django_otp.plugins.otp_totp.TOTPDevice`
* `backup_codes` (hash) salvos
* audit log: `user.mfa_enabled`

---

# 5. LOGIN COM MFA

## Passos

1. usuário digita email + senha
2. sistema valida credenciais
3. sistema retorna `{ requires_mfa: true, mfa_token: "..." }` (token temporário, 5 min)
4. frontend exibe tela de MFA
5. usuário digita código TOTP de 6 dígitos OU 1 backup code
6. backend valida — se OK, emite `access_token` + `refresh_token` JWT
7. backup code usado é invalidado

---

## Rate limiting

* 5 tentativas erradas → bloqueio de 15 minutos
* 3 bloqueios em 24h → conta bloqueada, requer reset

---

# 6. BACKUP CODES

* 8 códigos gerados na ativação
* armazenados em hash (bcrypt) no backend
* cada um pode ser usado **uma única vez**
* usuário pode regenerar (invalida os antigos) a qualquer momento
* exibidos uma única vez — após isso, só substituição
* recomendado salvar em gerenciador de senhas

---

# 7. RECUPERAÇÃO DE ACESSO

## Cenário A — Usuário ainda tem backup codes

1. login com email + senha
2. tela de MFA
3. clica em "Usar backup code"
4. digita 1 código
5. sistema valida, invalida o código, libera acesso
6. recomenda regenerar backup codes

---

## Cenário B — Sem backup codes, mas tem acesso ao email

1. login com email + senha
2. tela de MFA → "Não tenho acesso ao app nem aos códigos"
3. sistema envia link de recuperação ao email cadastrado
4. usuário clica no link (válido 30 min)
5. token de recuperação valida
6. sistema **desativa MFA temporariamente** e força reativação
7. usuário configura novo TOTP
8. audit log + alerta de segurança ao admin do tenant

---

## Cenário C — Admin do tenant sem acesso ao email

1. abre ticket de suporte com CS Clinix
2. suporte valida identidade (videoconferência + documento)
3. admin Clinix executa reset via comando admin
4. evento crítico: `user.mfa_reset_by_clinix`
5. alerta ao tenant em todos os canais

---

# 8. ROTAÇÃO

* recomenda-se rotacionar MFA a cada 12 meses
* sistema lembra o usuário no aniversário
* não força (rotação compulsória prejudica usabilidade)

---

# 9. ESCALATION DE OBRIGATORIEDADE

Quando admin do tenant torna MFA obrigatório para uma role:

1. configuração salva
2. todos os usuários atuais sem MFA recebem notificação
3. próximo login força ativação antes de prosseguir
4. tela bloqueia operações até MFA configurado
5. prazo de 7 dias para ativação (configurável)
6. após o prazo, usuário não consegue logar até ativar

---

# 10. AUDITORIA

Eventos registrados:

* `user.mfa_enabled`
* `user.mfa_login_succeeded`
* `user.mfa_login_failed`
* `user.backup_code_used`
* `user.mfa_disabled` (sempre crítico)
* `user.mfa_reset_via_email`
* `user.mfa_reset_by_clinix` (CS)
* `user.mfa_locked_due_to_attempts`

---

# 11. INTEGRAÇÃO COM SESSÃO

* JWT inclui claim `mfa: true | false`
* operações críticas (alterar permissões, ver auditoria, exportar dados) exigem `mfa: true`
* sessão sem MFA quando exigido recebe 403 com instrução de reautenticar

---

# 12. PUSH NOTIFICATION (FASE 4)

App mobile pode usar push para approvals (futuro):

* tentativa de login em web → push para app
* usuário aprova com biometria
* substitui código TOTP digitado

---

# 13. SMS COMO FALLBACK (FASE 2)

* opcional, mais caro e menos seguro que TOTP
* utilizado apenas para usuários sem smartphone
* requer ativação explícita do tenant

---

# 14. SSO (FASE 6)

* clientes enterprise podem usar SSO (SAML/OIDC) — MFA do IdP substitui o do Clinix

---

# 15. KPIs

* % de usuários com MFA ativo
* tempo médio para ativação após convite
* nº de tentativas falhas / dia
* nº de resets por mês (por motivo)

---

# 16. O QUE EVITAR

* permitir disable de MFA sem reautenticação
* mostrar segredo TOTP em texto claro após ativação
* armazenar backup codes em texto claro
* permitir mesmo código TOTP duas vezes (replay)
* expor `mfa_secret` em logs ou Sentry (scrubbing obrigatório)

---

# 17. REGRAS DE SEGURANÇA

* todo segredo TOTP encrypted no banco
* backup codes em bcrypt
* validação server-side (frontend só renderiza UI)
* operações de MFA sempre em HTTPS
* rate limiting em todos endpoints de MFA

---

# 18. EXEMPLO REAL

Cenário: **Dr. Sergio Santana** (fisio) ativa MFA no segundo dia de uso.

1. Configurações > Segurança > Ativar MFA
2. Escaneia QR code com Authy
3. Digita "473829" para confirmar
4. Salva 8 backup codes no 1Password
5. Próximo login: email + senha → tela MFA → digita código → entra
6. 3 meses depois perde celular
7. Login com email + senha → "Não tenho acesso" → recebe link no email
8. Reativa MFA com novo dispositivo
9. Backup codes regenerados

---

# 19. REFERÊNCIAS

* `docs/auth.md`
* `docs/auditoria.md`
* `tasks/sprint-02.md`
* `examples/fluxo-onboarding.md`
