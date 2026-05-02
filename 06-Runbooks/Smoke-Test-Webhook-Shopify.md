---
title: "Runbook — Smoke test webhook Shopify (HMAC + replay)"
tags:
  - runbook
  - shopify
  - webhook
  - smoke-test
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-05-01
---

# 📖 Runbook: Smoke test webhook Shopify (HMAC + replay)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Após deploy de nova versão da Edge Function `shopify-webhooks`
> - Após rotação do `SHOPIFY_CLIENT_SECRET`
> - Antes de cada conexão de tenant em produção
> - Como sanity check após migração de infra

## ✅ Pré-requisitos

- [ ] Supabase Edge Functions deployadas (incluindo `shopify-webhooks`)
- [ ] `SHOPIFY_CLIENT_SECRET` setado nos secrets do Supabase ([[Setar-Secret-EdgeFunction]])
- [ ] Repo local em `C:\Users\rianf\projetos\sistex\`
- [ ] Node.js disponível
- [ ] Variável `SHOPIFY_CLIENT_SECRET` no shell local (igual à do Supabase)

## 🛠️ Passos

### 1. Definir secret na sessão do PowerShell

```powershell
$env:SHOPIFY_CLIENT_SECRET = "<secret-real-do-app-Partners>"
```

> [!important] Mesmo secret do Supabase
> O smoke test calcula HMAC localmente com este secret e envia para a Edge Function — se divergir, o teste 1 falha.

### 2. Rodar o script de smoke test

```powershell
node C:\Users\rianf\projetos\sistex\scripts\smoke-test-shopify-webhook.mjs
```

### 3. Verificar saída esperada

```
✅ Test 1: HMAC válido → 200
✅ Test 2: HMAC inválido → 401
✅ Test 3: Replay (timestamp antigo) → 401
```

**Os 3 cenários cobrem:**

| Teste | O que valida |
|---|---|
| 1 | Caminho feliz: HMAC computado corretamente, replay window OK → aceita |
| 2 | HMAC fail: assinatura adulterada → rejeita 401 |
| 3 | Replay: timestamp > 10min ([[../05-Decisoes/004-replay-protection-10min|ADR-004]]) → rejeita 401 |

## ✔️ Validação final

- [ ] 3/3 testes passaram
- [ ] Logs do Supabase Edge Functions não mostraram erros 5xx no período do teste

## 🐞 Troubleshooting

### Sintoma: Test 1 → 401 ("HMAC inválido")
**Causa provável:** secret local difere do secret no Supabase.
**Correção:**
1. Listar secrets atuais: `supabase --workdir C:\Users\rianf\projetos\sistex secrets list` (mostra digest)
2. Pegar valor real do Partners Dashboard (ou 1Password)
3. Re-setar via [[Setar-Secret-EdgeFunction]]
4. Re-rodar smoke test
Ver [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]].

### Sintoma: Test 1 → 5xx (não 200, não 401)
**Causa provável:** Edge Function `shopify-webhooks` com bug ou não deployada.
**Correção:**
1. `supabase functions deploy shopify-webhooks`
2. Ver logs: dashboard Supabase → Functions → shopify-webhooks → Logs
3. Re-rodar smoke test

### Sintoma: Test 2 → 200 (deveria ser 401)
**Causa provável:** validação HMAC desligada / quebrada — **GRAVE**.
**Correção:** parar tudo, ver `_shared/hmac.ts` — algum refactor pode ter quebrado validação. Não fazer deploy até corrigir.

### Sintoma: Test 3 → 200 (deveria ser 401)
**Causa provável:** replay protection desligada.
**Correção:** verificar `_shared/replay_protection.ts` e que está sendo chamado em `shopify-webhooks`.

## 📞 Escalar para

- Se algum teste falhar e a correção não for óbvia: Rian
- Antes de demo: **NUNCA aceitar** smoke test < 3/3 em produção

## 📝 Histórico de execuções

| Data | Operador | Resultado | Observações |
|---|---|---|---|
| 2026-04-30 | Rian | 3/3 ✅ (com secret temp) | Pré-migração |
| 2026-05-01 | Rian | 3/3 ✅ | Pós-migração com secret real do app Partners |

## 🔗 Relacionado

- [[../05-Decisoes/004-replay-protection-10min|ADR-004]]
- [[Conectar-Shopify-Tenant]]
- [[Setar-Secret-EdgeFunction]]
- [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]]
- Script: `C:\Users\rianf\projetos\sistex\scripts\smoke-test-shopify-webhook.mjs`
