---
title: "Lesson — Supabase Free tier: rate limit de email é 3-4/h e bloqueia signups"
tags:
  - lesson-learned
  - supabase
  - auth
  - rate-limit
created: 2026-05-01
updated: 2026-05-01
status: ativo
severidade: alta
data-incidente: 2026-05-01
---

# 💡 Lesson Learned: Supabase Free tier limita 3-4 emails/h e bloqueia signups

> [!danger] Severidade: **alta**
> Quando aconteceu: **2026-05-01**, durante onboarding inicial após migração.

## 🐞 Problema enfrentado

**Sintoma:**
Tentativas de cadastro do Rian no SISTEX recém-migrado retornavam sucesso na UI, mas o **email de confirmação não chegava**. Após algumas tentativas, signups simplesmente paravam de gerar email.

**Impacto:**
- Rian não conseguia confirmar conta → não logava → não podia validar a migração end-to-end.
- Bloqueou onboarding do super_admin no momento crítico (T-4 dias da demo).
- Nenhum erro óbvio na UI — silencioso.

**Como descobrimos:**
Logs do Supabase Dashboard → **Auth** mostraram erro `rate_limit_exceeded` no envio de emails.

## 🔍 Causa raiz

> [!important] Supabase Free tier limita 3-4 emails/h pelo SMTP default
> O SMTP que vem grátis no Free tier é **de demo**, não pra uso real. Documentação Supabase deixa claro mas não destaca o número exato — ficamos sabendo na prática.

**Por que aconteceu:**
- Várias tentativas de signup (testando) consumiram o quota da hora
- Supabase silenciosamente parou de enviar emails sem retornar erro pra UI

**Por que não pegamos antes:**
- Projeto novo, primeiros signups
- Não checamos antes os limites do Free tier para email

## 🛠️ Solução aplicada

**Workaround (imediato) via Management API:**

1. Confirmar email manualmente via SQL:
   ```sql
   UPDATE auth.users
   SET email_confirmed_at = NOW()
   WHERE email = 'a.ordem.projeto@gmail.com';
   ```
   (Não usar `confirmed_at` — é coluna gerada, ver [[Supabase-confirmed-at-eh-coluna-gerada]].)

2. Setar senha via SQL com `crypt()`:
   ```sql
   UPDATE auth.users
   SET encrypted_password = crypt('Sistex2026!', gen_salt('bf'))
   WHERE email = 'a.ordem.projeto@gmail.com';
   ```

3. **Solução definitiva:** ativar `mailer_autoconfirm` via PATCH no Management API:
   ```
   PATCH /v1/projects/{ref}/config/auth
   { "mailer_autoconfirm": true }
   ```
   Com isso, próximos signups (Erick, Stenio) **não pegam rate limit** porque não dependem de email pra confirmar.

**Tempo para resolver:** ~15 minutos (depois que identificamos a causa).

## 🛡️ Prevenção futura

- [x] `mailer_autoconfirm: true` ativado para todos os ambientes Free tier
- [ ] Quando passar para tier pago OU configurar SMTP custom (Resend gratuito tem 100/dia), reativar email confirm
- [ ] Adicionar runbook [[../06-Runbooks/Resetar-Senha-User-via-SQL]] para reset emergencial
- [ ] Documentar em onboarding de tenant: senha inicial setada manualmente, **trocar logo**

## 📚 O que aprendemos

> Free tier de Supabase **não é production-ready para auth com volume real**. Pra demo/MVP funciona com `mailer_autoconfirm`. Para produção, configurar SMTP custom (Resend) ou subir pra tier pago.

> O erro foi **silencioso** — UI mostrava signup OK. Sempre checar logs Supabase Auth quando "email não chega".

## 🔗 Relacionado

- [[Supabase-confirmed-at-eh-coluna-gerada]]
- [[../06-Runbooks/Resetar-Senha-User-via-SQL]]
- [[../06-Runbooks/Promover-User-SuperAdmin]]
- Sessão: [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
