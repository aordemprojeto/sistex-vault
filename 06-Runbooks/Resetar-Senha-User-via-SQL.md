---
title: "Runbook — Resetar senha de user via SQL (Management API)"
tags:
  - runbook
  - supabase
  - auth
  - sql
  - emergencia
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-05-01
---

# 📖 Runbook: Resetar senha de user via SQL (Management API)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Frontend ainda não tem tela "Alterar senha" e o user precisa entrar
> - Email de recovery não chega (rate limit Free tier — ver [[../07-Lessons-Learned/Supabase-rate-limit-email-3-por-hora]])
> - User precisa de senha emergencial (ex: Stenio antes da demo)
> - Reset administrativo justificado

> [!danger] Operação privilegiada
> Setar senha por SQL bypassa o fluxo de email-based reset. **Auditar quem fez e por quê** em [[../04-Sessoes/]].

## ✅ Pré-requisitos

- [ ] **Supabase PAT** (`sbp_...`) salvo em 1Password
- [ ] **Project ref:** `eppxxuostbtkuuftgasp`
- [ ] **Email do user-alvo** confirmado
- [ ] **PowerShell** disponível (Windows do Rian)

## 🛠️ Passos

### 1. Setar PAT como variável de sessão

```powershell
$pat = "<PAT-SBP-do-1Password>"
$projectRef = "eppxxuostbtkuuftgasp"
```

> [!tip] Por que via Management API e não Dashboard SQL Editor
> Dashboard SQL Editor exige login com 2FA cada vez; Management API é direto via PAT, sem sessão de browser.

### 2. (Opcional) Confirmar email do user (se ainda não confirmado)

`auth.users.confirmed_at` é coluna gerada — atualizar `email_confirmed_at` ([[../07-Lessons-Learned/Supabase-confirmed-at-eh-coluna-gerada]]):

```powershell
$body = @{ query = @"
UPDATE auth.users
SET email_confirmed_at = NOW()
WHERE email = 'user@example.com';
"@ } | ConvertTo-Json

Invoke-RestMethod -Method POST `
  -Uri "https://api.supabase.com/v1/projects/$projectRef/database/query" `
  -Headers @{ "Authorization" = "Bearer $pat"; "Content-Type" = "application/json" } `
  -Body $body
```

**Validação:** retorno JSON com `{"data":[]}` (UPDATE não retorna rows).

### 3. Setar senha nova com `crypt()`

A extensão `pgcrypto` já está habilitada no Supabase.

```powershell
$novaSenha = "<senha-temporaria-forte>"

$body = @{ query = @"
UPDATE auth.users
SET encrypted_password = crypt('$novaSenha', gen_salt('bf'))
WHERE email = 'user@example.com';
"@ } | ConvertTo-Json

Invoke-RestMethod -Method POST `
  -Uri "https://api.supabase.com/v1/projects/$projectRef/database/query" `
  -Headers @{ "Authorization" = "Bearer $pat"; "Content-Type" = "application/json" } `
  -Body $body
```

> [!warning] Cuidado com escape de caracteres
> Se a senha contém aspas, `$`, ou caracteres especiais do PowerShell, escapar com backtick (`) ou usar here-string `@'...'@`.

**Validação:** retorno JSON sem erro.

### 4. Comunicar a senha de forma segura

> [!danger] Nunca por chat
> Senha temporária deve ir por canal seguro (1Password share, Signal, etc.). **Anotar em [[../04-Sessoes/]] que foi compartilhada — vira pendência de rotação.**

### 5. User testa login

```
URL: https://portalsistex.com
Email: user@example.com
Senha: <a senha temporária>
```

**Validação:** login bem-sucedido + sidebar carrega conforme role do user.

### 6. Forçar troca da senha o quanto antes

Frontend ainda não tem tela de alteração — quando tiver, exigir troca no primeiro login. Por enquanto, registrar pendência:

- [ ] Adicionar TODO em [[../01-Contexto/CLAUDE]] seção 14 (Importantes) — *trocar senha do user X*

## ✔️ Validação final

- [ ] User loga com nova senha
- [ ] Sidebar/permissões corretas (depende de role em `org_members`)
- [ ] Pendência de rotação registrada na sessão atual

## 🐞 Troubleshooting

### Sintoma: erro `function crypt(...) does not exist`
**Causa provável:** extensão `pgcrypto` não habilitada (raro em Supabase, mas possível).
**Correção:**
```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```

### Sintoma: erro `column "confirmed_at" can only be updated to DEFAULT`
**Causa provável:** tentou UPDATE em `confirmed_at` em vez de `email_confirmed_at`.
**Correção:** ver [[../07-Lessons-Learned/Supabase-confirmed-at-eh-coluna-gerada]].

### Sintoma: 401 da Management API
**Causa provável:** PAT expirado, rotacionado, ou typo.
**Correção:** gerar novo PAT em https://supabase.com/dashboard/account/tokens, salvar em 1Password.

## 📞 Escalar para

- Se PAT estiver indisponível ou comprometido: Rian (regenerar)

## 📝 Histórico de execuções

| Data | Operador | User-alvo | Resultado | Observações |
|---|---|---|---|---|
| 2026-05-01 | Rian | a.ordem.projeto@gmail.com | ✅ ok | Senha `Sistex2026!` — vazada em chat, **rotacionar** |

## 🔗 Relacionado

- [[../07-Lessons-Learned/Supabase-rate-limit-email-3-por-hora]]
- [[../07-Lessons-Learned/Supabase-confirmed-at-eh-coluna-gerada]]
- [[Promover-User-SuperAdmin]]
- [[../01-Contexto/CLAUDE]] — seção 17 (NUNCA expor secrets em chat)
