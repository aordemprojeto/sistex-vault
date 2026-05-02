---
title: "Lesson — Supabase auth.users.confirmed_at é coluna gerada — UPDATE em email_confirmed_at"
tags:
  - lesson-learned
  - supabase
  - postgres
  - auth
  - sql
created: 2026-05-01
updated: 2026-05-01
status: ativo
severidade: baixa
data-incidente: 2026-05-01
---

# 💡 Lesson Learned: `auth.users.confirmed_at` é coluna gerada — atualizar `email_confirmed_at`

> [!danger] Severidade: **baixa** (atrito, fix imediato)
> Quando aconteceu: **2026-05-01**, ao tentar confirmar email manualmente via SQL para contornar rate limit.

## 🐞 Problema enfrentado

**Sintoma:**
SQL falhava ao tentar:

```sql
UPDATE auth.users SET confirmed_at = NOW() WHERE email = '...';
```

Erro Postgres: `column "confirmed_at" can only be updated to DEFAULT` (ou similar — coluna gerada não aceita UPDATE direto).

**Impacto:**
Atrasou em alguns minutos a confirmação manual do email do Rian (que estava bloqueado pelo rate limit do Supabase Free tier).

**Como descobrimos:**
Mensagem de erro Postgres clara após primeira tentativa.

## 🔍 Causa raiz

> [!important] `confirmed_at` é GENERATED COLUMN baseada em `email_confirmed_at` e `phone_confirmed_at`
> Não pode receber UPDATE direto. É derivada automaticamente das outras duas colunas.

**Por que aconteceu:**
- Schema do Supabase Auth tem essa coluna calculada
- Documentação não destaca

**Por que não pegamos antes:**
- Primeira vez manipulando `auth.users` via SQL direto
- Esperávamos schema "plano" (todas as colunas editáveis)

## 🛠️ Solução aplicada

**SQL correto:**

```sql
UPDATE auth.users
SET email_confirmed_at = NOW()
WHERE email = 'a.ordem.projeto@gmail.com';
```

`confirmed_at` é populada automaticamente. Resultado idêntico para fluxos de auth do Supabase.

**Tempo para resolver:** ~5 minutos (depois da mensagem de erro).

## 🛡️ Prevenção futura

- [x] Documentado no runbook [[../06-Runbooks/Resetar-Senha-User-via-SQL]]
- [ ] Ao tocar em `auth.users`, sempre rodar `\d auth.users` primeiro pra ver quais colunas são geradas
- [x] Mencionado em [[../01-Contexto/CLAUDE]] seção "Lessons Learned > Supabase"

## 📚 O que aprendemos

> **Postgres `GENERATED COLUMN`** é silenciosa em schemas legados — pode parecer coluna normal até você tentar UPDATE. Em schemas Supabase Auth, sempre olhar `\d` antes de manipular.
>
> Atualizar `email_confirmed_at` (e `phone_confirmed_at` se aplicável) cobre todos os cenários de "marcar como confirmado".

## 🔗 Relacionado

- [[Supabase-rate-limit-email-3-por-hora]] — contexto de quando precisamos disso
- [[../06-Runbooks/Resetar-Senha-User-via-SQL]]
- [[../06-Runbooks/Promover-User-SuperAdmin]]
