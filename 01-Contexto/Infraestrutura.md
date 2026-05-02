---
title: Infraestrutura
tags:
  - contexto
  - infra
  - urls
created: 2026-05-01
updated: 2026-05-01
status: active
---

# 🌐 Infraestrutura — URLs, IDs, Refs

> [!warning] Sem secrets reais aqui
> Este arquivo só contém URLs, IDs e nomes de secrets. Valores sensíveis vivem no 1Password. Marcadores `<no-1password>` indicam onde buscar.

## Frontend (Vercel)

| Atributo | Valor |
|---|---|
| Provider | Vercel |
| Org/team | `aordemprojeto-5068` (Hobby/Free, conta YNTHEGRA) |
| Projeto | `sistex` |
| URL primária | `https://portalsistex.com` |
| URL secundária | `https://sistex.vercel.app` |
| URL `www` | `https://www.portalsistex.com` (redirect 307 → apex) |
| Repo conectado | `YNTHEGRA/sistex` (branch `main`) |
| Auto-deploy | Sim, push em `main` dispara build |

## Backend (Supabase)

| Atributo | Valor |
|---|---|
| Org | YNTHEGRA.Org (Free) |
| Project ref | `eppxxuostbtkuuftgasp` |
| Project URL | `https://eppxxuostbtkuuftgasp.supabase.co` |
| Anon key (público) | `sb_publishable_TxSjFY2FLS2YdO7gBo4LzA_gNrKnbeP` |
| Service role | `<no-1password>` (formato `sb_secret_...`) |
| DB password | `<no-1password>` |
| Region | South America (São Paulo) — `sa-east-1` |
| Dashboard | https://supabase.com/dashboard/project/eppxxuostbtkuuftgasp |

## Domínio (Hostinger)

| Atributo | Valor |
|---|---|
| Domínio | `portalsistex.com` |
| Registrar | Hostinger |
| Nameservers | `byte.dns-parking.com` + `pixel.dns-parking.com` |
| `A @` | `76.76.21.21` |
| `CNAME www` | `cname.vercel-dns.com` |
| `TXT _vercel` | `vc-domain-verify=portalsistex.com,...` |
| `TXT _vercel.www` | `vc-domain-verify=www.portalsistex.com,...` |

ADR: [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]

## Repositório (GitHub)

| Atributo | Valor |
|---|---|
| Org | YNTHEGRA |
| Repo | `YNTHEGRA/sistex` |
| URL | https://github.com/YNTHEGRA/sistex |
| Visibilidade | Privado |
| Branch principal | `main` |

## App Shopify Partners

| Atributo | Valor |
|---|---|
| Org Partners | YNTHEGRA / A ORDEM |
| Email Partners | `a.ordem.projeto@gmail.com` |
| Email contato API | `ynthegra@gmail.com` |
| Nome do app | Sistex |
| Client ID | `5254a4d49feb9801b1cbe8263e98b76c` |
| Client Secret | `<no-1password>` (formato `shpss_...`) |
| App URL | `https://sistex.vercel.app` |
| Redirect URI | `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-connect-callback` |
| Scopes | `read_orders, read_products` |
| API webhooks version | 2026-04 |

ADRs: [[../05-Decisoes/006-shopify-readonly-scopes|ADR-006]] · [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]]

## Secrets em Supabase Edge Functions

| Secret | Status |
|---|---|
| `ENCRYPTION_MASTER_KEY` | ✅ Setado (32 bytes random base64) |
| `SHOPIFY_CLIENT_ID` | ✅ Setado |
| `SHOPIFY_CLIENT_SECRET` | ✅ Setado (real) |
| `APP_BASE_URL` | ✅ `https://sistex.vercel.app` |
| `MP_WEBHOOK_SECRET` | ⏳ Pendente |
| `ML_WEBHOOK_SECRET` | ⏳ Pendente |
| `SHOPEE_WEBHOOK_SECRET` | ⏳ Pendente |
| `ME_WEBHOOK_SECRET` | ⏳ Pendente |
| `BLING_CLIENT_ID/SECRET` | ⏳ Pendente |
| `SUPABASE_*` | ✅ Auto-managed |

Runbook: [[../06-Runbooks/Setar-Secret-EdgeFunction]].

## Vercel Environment Variables

| Var | Valor |
|---|---|
| `VITE_SUPABASE_URL` | `https://eppxxuostbtkuuftgasp.supabase.co` |
| `VITE_SUPABASE_PUBLISHABLE_KEY` | `sb_publishable_TxSjFY2FLS2YdO7gBo4LzA_gNrKnbeP` |
| `ID_DO_PROJETO_SUPABASE_VITE` | `eppxxuostbtkuuftgasp` (nome em PT herdado do Lovable) |

## Personal Access Tokens

| Provider | Status |
|---|---|
| Supabase PAT (`sbp_...`) | ✅ Salvo em 1Password — **rotacionar** (compartilhado em chat) |
| GitHub PAT | ❌ Não criado |
| Vercel CLI | Instalado (`v52.0.0`) sem login interativo |

## Organizações em DB (`public.organizations`)

| ID | Nome | Tipo | Status |
|---|---|---|---|
| `24cf8d27-d291-41b8-92d8-c0b3ff6d638b` | YNTHEGRA | admin | approved |
| `37c2fa01-adca-4320-84eb-65c46874126f` | Ferstex | supplier | approved |

## Users iniciais (`auth.users`)

| Email | User ID | Roles |
|---|---|---|
| `a.ordem.projeto@gmail.com` | `e9f5e5a2-9825-4862-afb1-df3976792003` | super_admin (YNTHEGRA) + supplier_admin (Ferstex) |

## Repo local

```
C:\Users\rianf\projetos\sistex\
├── CLAUDE.md
├── src/
├── supabase/
└── scripts/smoke-test-shopify-webhook.mjs
```

## 🔗 Relacionado

- [[Arquitetura]] — stack
- [[CLAUDE]] — contexto mestre
- [[../06-Runbooks/]] — procedimentos operacionais
- [[../05-Decisoes/]] — ADRs de infra
