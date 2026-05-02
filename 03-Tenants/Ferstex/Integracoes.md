---
title: Ferstex — Integrações
tags:
  - tenant
  - ferstex
  - integracoes
created: 2026-05-01
updated: 2026-05-01
status: parcial
---

# 🔌 Ferstex — Integrações

## Status geral

| Integração | Status | Bloqueio | Runbook |
|---|---|---|---|
| Shopify | ⏳ Aguardando | Domínio `xxx.myshopify.com` da loja | [[../../06-Runbooks/Conectar-Shopify-Tenant]] |
| Bling | ⏳ Pendente | Stenio reconectar via painel | — |
| Melhor Envio | ⏳ Pendente | Stenio reconectar via painel | — |
| Mercado Pago | 🔴 Futuro | Pós-demo | — |
| Mercado Livre | 🔴 Futuro | Pós-demo | — |
| Shopee | 🔴 Futuro | Pós-demo | — |

## Shopify

| Campo | Valor |
|---|---|
| Status | ⏳ Aguardando domínio da loja do Stenio |
| Domínio Shopify (`xxx.myshopify.com`) | {{pendente-stenio}} |
| Domínio público (visível ao cliente) | `ferstex.com.br` (provável) |
| App SISTEX | "Sistex" no Shopify Partners |
| Client ID | `5254a4d49feb9801b1cbe8263e98b76c` |
| Scopes | `read_orders, read_products` ([[../../05-Decisoes/006-shopify-readonly-scopes|ADR-006]]) |
| API version | 2026-04 |
| Webhooks ativos | Pedido criado / Pedido pago (após conexão) |

> [!warning] Loja não conectada ainda
> Falta o domínio `xxx.myshopify.com` da loja Stenio. Sem ele não é possível iniciar OAuth. Pedido enviado via WhatsApp.

## Bling

| Campo | Valor |
|---|---|
| Status | ⏳ Pendente reconexão |
| Tipo de auth | OAuth 2.0 |
| Uso | Emissão de NF-e e controle de estoque |
| Secret | `BLING_CLIENT_ID/SECRET` ⏳ não setado nos secrets do Supabase |
| Conta Bling Ferstex | {{pendente-stenio}} |

> [!info] Reconexão pelo painel
> Quando Stenio logar como `supplier_admin` da Ferstex no SISTEX, ele conecta o Bling pelo wizard de Integrações. Sem necessidade de manipular secrets do lado da YNTHEGRA — só `BLING_CLIENT_ID/SECRET` global precisa estar setado uma vez.

## Melhor Envio

| Campo | Valor |
|---|---|
| Status | ⏳ Pendente reconexão |
| Tipo de auth | OAuth 2.0 |
| Uso | Cotação de frete + emissão de etiqueta |
| Secret | `ME_WEBHOOK_SECRET` ⏳ não setado |
| Conta Melhor Envio Ferstex | {{pendente-stenio}} |

> [!info] Crítico para a demo
> Etiqueta gerada com 1 clique é parte do fluxo único da demo (ver [[../../02-Sprints/MVP-Stenio-05mai]]).

## Como o tenant conecta cada integração

Ver runbook genérico [[../../06-Runbooks/Conectar-Shopify-Tenant]] (mesma lógica para Bling e Melhor Envio).

```
1. Stenio loga em https://portalsistex.com (super_admin/supplier_admin)
2. Vai em Integrações → escolhe Bling / Melhor Envio / Shopify
3. Clica "Conectar" → redireciona para OAuth da plataforma
4. Autoriza com credenciais Ferstex
5. Volta pro SISTEX com token cifrado AES-GCM v2 (ADR-003) salvo em DB
```

## 🔗 Relacionado

- [[README|Tenant Ferstex]]
- [[Catalogo-SKUs]] — depende de Shopify estar conectado
- [[../../06-Runbooks/Conectar-Shopify-Tenant]]
- [[../../05-Decisoes/006-shopify-readonly-scopes|ADR-006]]
- [[../../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]] — criptografia dos tokens
