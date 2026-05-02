---
title: "Runbook — Deploy de Edge Function (Supabase)"
tags:
  - runbook
  - supabase
  - edge-functions
  - deploy
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: media
ultima-execucao: 2026-05-01
---

# 📖 Runbook: Deploy de Edge Function (Supabase)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Modificou código de uma function em `supabase/functions/<name>/`
> - Adicionou nova function
> - Atualizou dependências em `_shared/`
> - Após troca de secret que afeta uma function específica

## ✅ Pré-requisitos

- [ ] Supabase CLI **binário nativo** instalado (não npm — ver [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]])
- [ ] `SUPABASE_ACCESS_TOKEN` (PAT) disponível em 1Password
- [ ] Projeto linkado: `supabase link --project-ref eppxxuostbtkuuftgasp`
- [ ] Repo local em `C:\Users\rianf\projetos\sistex\`

## 🛠️ Passos

### 1. Setar PAT como env var

```powershell
$env:SUPABASE_ACCESS_TOKEN = "<PAT-do-1Password>"
```

### 2. Linkar projeto (uma vez por máquina)

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex link --project-ref eppxxuostbtkuuftgasp --yes
```

**Validação:** mensagem "Linked to project eppxxuostbtkuuftgasp".

### 3. Deploy de UMA function específica

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex functions deploy <nome-da-function>
```

Exemplo:

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex functions deploy shopify-webhooks
```

**Validação:** output indica `Function deployed successfully`.

### 4. Deploy de TODAS as functions (raro, ~10min para 38 functions)

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex functions deploy
```

> [!info] Quando usar
> - Após mudança em `_shared/` que afeta múltiplas functions
> - Após migração de infra (foi o caso em 2026-04-30)

### 5. Verificar deploy via curl/Invoke-RestMethod

```powershell
curl -sI "https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/<nome-da-function>" --max-time 10
```

Esperado: status `200`, `401` (auth requerida), ou `405` (method not allowed) — mas **não** `404` (function não existe).

### 6. Testar lógica end-to-end

Para `shopify-webhooks`: rodar [[Smoke-Test-Webhook-Shopify]].

Para outras: chamar via curl com payload válido OU via UI do SISTEX que dispara a function.

## ✔️ Validação final

- [ ] Function listada em https://supabase.com/dashboard/project/eppxxuostbtkuuftgasp/functions
- [ ] Status `Active`
- [ ] Logs sem erro 5xx no período pós-deploy

## 🐞 Troubleshooting

### Sintoma: `supabase: command not found`
**Causa provável:** PATH não inclui `C:\Users\rianf\bin\` OU instalação via npm em vez de binário.
**Correção:** [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]].

### Sintoma: `Error: not logged in` ou `401 Unauthorized`
**Causa provável:** PAT não setado ou expirado.
**Correção:**
```powershell
$env:SUPABASE_ACCESS_TOKEN = "<PAT-novo>"
```

### Sintoma: deploy "successful" mas a function antiga ainda responde
**Causa provável:** cache de CDN Supabase (raro, ~30s).
**Correção:** aguardar 1min e tentar novamente. Se persistir, fazer `--no-verify-jwt` flag check ou re-deploy.

### Sintoma: erro de syntax/runtime no Deno na hora do deploy
**Causa provável:** Edge Functions rodam Deno (não Node) — incompatibilidade de import.
**Correção:** verificar imports usando `https://deno.land/...` ou `npm:...` (não require()), Deno é estrito sobre paths.

## 📞 Escalar para

- Se a function deployar mas não responder em produção: ver logs no Dashboard, depois Rian

## 📝 Histórico de execuções

| Data | Operador | Function(s) | Resultado |
|---|---|---|---|
| 2026-04-30 | Rian | TODAS (38) — pós-migração | ✅ ok |
| 2026-05-01 | Rian | shopify-webhooks (re-deploy) | ✅ ok |

## 🔗 Relacionado

- [[Aplicar-Migrations-Supabase]]
- [[Setar-Secret-EdgeFunction]]
- [[Smoke-Test-Webhook-Shopify]]
- [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]]
