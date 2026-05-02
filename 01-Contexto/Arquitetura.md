---
title: Arquitetura
tags:
  - contexto
  - arquitetura
  - tecnico
created: 2026-05-01
updated: 2026-05-01
status: active
---

# 🏗️ Arquitetura — SISTEX

## Diagrama mental

```
┌─────────────────────────────────────────────────────┐
│  USUÁRIOS                                           │
│  • Cliente final (compra na loja Shopify do Stenio) │
│  • Drop/revendedor (paga R$130/mês)                 │
│  • Admin Ferstex (Stenio — gerencia produtos)       │
│  • Super-admin YNTHEGRA (Rian/Erick)                │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  FRONTEND — React 18 + Vite + TS                    │
│  Hospedado em: Vercel (conta aordemprojeto-5068)    │
│  URL: https://portalsistex.com                      │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  BACKEND — Supabase                                 │
│  Project ref: eppxxuostbtkuuftgasp                  │
│  • Postgres (45 migrations)                         │
│  • Edge Functions Deno (38 functions)               │
│  • Auth (email/password)                            │
│  • Storage (imagens de produto)                     │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  INTEGRAÇÕES EXTERNAS                               │
│  • Shopify (read-only)                              │
│  • Bling (OAuth) — NF-e e estoque                   │
│  • Melhor Envio (OAuth) — etiquetas                 │
│  • Mercado Pago (Marketplace)                       │
│  • Stripe (futuro — KYC)                            │
└─────────────────────────────────────────────────────┘
```

## Stack

### Frontend

| Camada | Tecnologia |
|---|---|
| Framework | React 18 + TypeScript |
| Bundler | Vite (SWC) |
| UI | shadcn/ui (Radix) + Tailwind CSS + styled-components |
| Roteamento | React Router DOM v6 |
| Server state | TanStack React Query |
| Formulários | React Hook Form + Zod |
| Gráficos | Recharts |
| PDF / XLS | jsPDF + jspdf-autotable / xlsx |
| Animação | Framer Motion |
| Testes | Vitest + Testing Library |
| Package manager | Bun (fallback npm) |
| i18n | Custom PT-BR |

### Backend

| Camada | Tecnologia |
|---|---|
| Database | Postgres (Supabase) |
| Edge Functions | Deno (Supabase Functions) |
| Auth | Supabase Auth (email/password) |
| Storage | Supabase Storage |
| Realtime | Supabase Realtime (não usado ainda) |

## Princípio multi-tenant

> [!important] Toda tabela operacional tem `org_id`
> FK pra `organizations.id`, RLS habilitada com policies que filtram por `get_user_org_ids(auth.uid())`. Super-admin YNTHEGRA bypassa via `is_super_admin()`.

ADRs: [[../05-Decisoes/001-multitenant-via-org-id|ADR-001]] · [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]]

## Princípio "infra na YNTHEGRA"

> [!danger] REGRA INVIOLÁVEL
> Toda conta de infraestrutura crítica é da YNTHEGRA, não de fornecedores ou drops. Se ver infra em conta de fornecedor, **migrar imediatamente**.

ADR: [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]

## Roles do enum `app_role`

```
super_admin       — YNTHEGRA platform
reseller_admin    — admin de revendedor
reseller_ops      — operação de revendedor (default novos users)
reseller_finance  — financeiro de revendedor
supplier_admin    — admin de fornecedor (ex: Stenio)
supplier_ops      — operação de fornecedor
supplier_finance  — financeiro de fornecedor
```

## Tipos e status de organização

```
Tipos:    admin | reseller | supplier
Status:   pending → review → approved → suspended
```

## Estrutura do repositório

```
sistex/
├── src/
│   ├── pages/          # 48 telas (rotas em PT-BR)
│   ├── components/     # ui, orders, catalog, ...
│   ├── hooks/          # 36+ custom hooks
│   ├── contexts/       # AuthContext
│   ├── integrations/   # Cliente e tipos Supabase
│   ├── lib/            # Utilitários (PDF, parser, pipeline)
│   ├── i18n/           # Traduções PT-BR
│   └── theme/          # Tema sidebar
├── supabase/
│   ├── config.toml     # Config functions (verify_jwt por function)
│   ├── migrations/     # 45 SQL migrations
│   └── functions/      # 38 Edge Functions
│       └── _shared/    # hmac, validation, encryption, audit, replay_protection, shopify
├── scripts/
│   └── smoke-test-shopify-webhook.mjs
├── CLAUDE.md           # Contexto Claude Code (auto-load)
└── .env                # VITE_SUPABASE_* (chaves públicas)
```

## Padrões de segurança (resumo)

- **HMAC** em todos os webhooks (`_shared/hmac.ts`)
- **Replay protection** janela 10min ([[../05-Decisoes/004-replay-protection-10min|ADR-004]])
- **Zod** em todos os endpoints (`_shared/validation.ts`)
- **AES-GCM v2 + HKDF** em tokens OAuth ([[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]])
- **RLS** em todas as tabelas
- **Idempotência** em pagamentos (`UNIQUE` em `idempotency_key`)
- **Wallet locks** com `pg_advisory_xact_lock`
- **Audit log** com `correlation_id` + `trace_id`

Detalhe completo: seção 12 do [[CLAUDE]].

## 🔗 Relacionado

- [[CLAUDE]] — contexto mestre
- [[Infraestrutura]] — URLs, IDs e secrets
- [[../05-Decisoes/]] — ADRs
- [[../06-Runbooks/]] — procedimentos
