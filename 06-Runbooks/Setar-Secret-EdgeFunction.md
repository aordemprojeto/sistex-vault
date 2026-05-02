---
title: "Runbook — Setar secret de Edge Function (Supabase secrets)"
tags:
  - runbook
  - supabase
  - secrets
  - edge-functions
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-05-01
---

# 📖 Runbook: Setar secret de Edge Function

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Configurar nova integração (ex: `BLING_CLIENT_SECRET`)
> - Rotacionar secret existente (ex: após `Refresh` no Shopify Partners — ver [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]])
> - Troca de URL base após migração (`APP_BASE_URL`)
> - Adicionar `ENCRYPTION_MASTER_KEY` em projeto novo

> [!danger] `ENCRYPTION_MASTER_KEY` é especial
> **NUNCA trocar `ENCRYPTION_MASTER_KEY` depois de tokens cifrados existirem** sem migração defensiva. Quebra todos os tokens OAuth no banco. Ver [[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]].

## ✅ Pré-requisitos

- [ ] Supabase CLI binário nativo
- [ ] `SUPABASE_ACCESS_TOKEN` (PAT) salvo
- [ ] Projeto linkado: `supabase link --project-ref eppxxuostbtkuuftgasp`
- [ ] Valor do secret salvo em 1Password

## 🛠️ Passos

### 1. Setar PAT

```powershell
$env:SUPABASE_ACCESS_TOKEN = "<PAT>"
```

### 2. Listar secrets atuais (mostra digest, não valor)

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex secrets list
```

Output: nome + hash (sha256). Útil para confirmar se algo já está setado e qual o digest atual.

### 3. Setar secret novo (ou atualizar existente)

**Sintaxe:** `secrets set "NOME=valor"` (aspas se valor contém caracteres especiais).

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex secrets set "SHOPIFY_CLIENT_SECRET=shpss_xxxxxxxxxxxxxxxxxxxxxxxx"
```

**Múltiplos secrets de uma vez:**

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex secrets set `
  "BLING_CLIENT_ID=foo" `
  "BLING_CLIENT_SECRET=bar" `
  "ME_WEBHOOK_SECRET=baz"
```

**Validação:** output `Finished setting secrets.`

### 4. Listar de novo e comparar digest

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex secrets list
```

O digest do secret atualizado deve ter mudado.

### 5. Edge Functions usam o novo valor automaticamente

> [!info] Sem redeploy necessário
> Functions leem `Deno.env.get('NOME')` em runtime. Próxima invocação já vê o novo valor (cold-start max ~1s).

### 6. Smoke test específico da integração

| Secret atualizado | Smoke test |
|---|---|
| `SHOPIFY_CLIENT_SECRET` | [[Smoke-Test-Webhook-Shopify]] |
| `APP_BASE_URL` | testar OAuth callback (gerar URL, ver se redirect bate) |
| `MP_WEBHOOK_SECRET` | trigger webhook MP teste |
| `BLING_*` | reconectar Bling pelo painel |
| `ME_WEBHOOK_SECRET` | trigger webhook Melhor Envio teste |

## ✔️ Validação final

- [ ] `secrets list` mostra digest atualizado
- [ ] Smoke test da integração passou
- [ ] Logs de Edge Function não mostram erro de "secret missing" ou "invalid secret"

## 🐞 Troubleshooting

### Sintoma: `Error: must be authenticated`
**Causa:** PAT ausente.
**Correção:** `$env:SUPABASE_ACCESS_TOKEN = "<PAT>"`.

### Sintoma: caracteres especiais quebrando o valor
**Causa:** `=`, `$`, espaço, aspas no secret.
**Correção:** envolver em aspas duplas; escapar com backtick (`` ` ``) no PowerShell:
```powershell
supabase secrets set "FOO=valor`$with`$dollars"
```

### Sintoma: secret atualizado, mas function ainda usa antigo
**Causa provável:** instância da function ainda quente com env vars antigas (raro, ~30s).
**Correção:** aguardar 1min ou re-deploy: `supabase functions deploy <nome>`.

### Sintoma: digest não mudou após `set`
**Causa:** valor passado é igual ao existente (provavelmente erro de copy-paste).
**Correção:** confirmar valor real no 1Password.

## 📞 Escalar para

- Se for `ENCRYPTION_MASTER_KEY` em projeto com tokens existentes: **PARAR**, não setar — escalar para Rian.

## 📝 Histórico de execuções

| Data | Operador | Secret(s) | Resultado |
|---|---|---|---|
| 2026-04-30 | Rian | `ENCRYPTION_MASTER_KEY` (random), `SHOPIFY_CLIENT_SECRET` (temp) | ✅ |
| 2026-05-01 | Rian | `SHOPIFY_CLIENT_SECRET` (real do Partners), `SHOPIFY_CLIENT_ID`, `APP_BASE_URL` | ✅ |

## 🔗 Relacionado

- [[Deploy-EdgeFunction]]
- [[Smoke-Test-Webhook-Shopify]]
- [[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]]
- [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]]
- [[../01-Contexto/Infraestrutura]] — lista atual de secrets
