---
title: "Runbook — Conectar loja Shopify de um tenant"
tags:
  - runbook
  - shopify
  - oauth
  - integracao
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: ""
---

# 📖 Runbook: Conectar loja Shopify de um tenant

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Um tenant supplier precisa conectar a loja Shopify dele ao SISTEX
> - Setup do MVP da Ferstex (loja Stenio)
> - Onboarding de novo supplier que vende via Shopify

## ✅ Pré-requisitos

- [ ] **Domínio interno da loja**: `xxx.myshopify.com` (não o domínio público `*.com.br`)
- [ ] **Tenant aprovado** em `public.organizations` (`status = 'approved'`)
- [ ] **User do tenant** com role `supplier_admin` (ver [[Promover-User-SuperAdmin]] análogo)
- [ ] **App Shopify Sistex** com Client ID e Secret setados nos secrets do Supabase ([[Setar-Secret-EdgeFunction]])
- [ ] **Frontend `https://portalsistex.com`** funcional

## 🛠️ Passos

### 1. Confirmar domínio Shopify do tenant

Pedir para o tenant: *"Qual o domínio Shopify interno da sua loja? Aquele formato `xxx.myshopify.com`."*

> [!tip] Onde o tenant encontra
> No admin Shopify: **Configurações → Domínios → "Domínio Shopify"** (não confundir com domínio público).

### 2. Tenant loga no SISTEX como `supplier_admin`

```
URL: https://portalsistex.com
Email: <email-do-tenant>
Senha: <senha-inicial-ou-trocada>
```

**Validação:** sidebar mostra menu de **CATÁLOGO**, **MINHAS LOJAS**, **OPERAÇÕES** (modo supplier).

### 3. Iniciar OAuth pelo painel

Tenant navega:

```
Painel → Integrações → Shopify → "Conectar loja"
```

Insere o domínio `xxx.myshopify.com` e clica **Conectar**.

**O que acontece por baixo:**
- Frontend chama Edge Function `shopify-connect-start`
- Backend gera URL OAuth com `state` cifrado
- Redireciona para `https://xxx.myshopify.com/admin/oauth/authorize?client_id=...&scope=read_orders,read_products&...`

**Validação:** browser redireciona para tela de autorização Shopify do tenant.

### 4. Tenant autoriza o app

Tela Shopify mostra: *"Sistex deseja acessar [...] Pedidos: somente leitura, Produtos: somente leitura"*. Tenant clica **Instalar app**.

**Validação:** redireciona de volta para `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-connect-callback?code=...`.

### 5. Backend processa callback

A Edge Function `shopify-connect-callback`:
1. Troca o `code` por `access_token` via `POST https://xxx.myshopify.com/admin/oauth/access_token`
2. Cifra o token com AES-GCM v2 ([[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]])
3. Salva em `shopify_connections` (org_id = tenant)
4. Registra audit log com `correlation_id`
5. Redireciona para `https://portalsistex.com/integrações/shopify?status=success`

**Validação:** UI mostra "Loja conectada" + lista de pedidos/produtos sincronizados.

### 6. Confirmar webhooks registrados

Backend registra webhooks Shopify automaticamente:

```sql
SELECT topic, address, status
FROM shopify_webhooks
WHERE org_id = '<tenant-org-id>';
```

Esperado: ao menos `orders/create` e `orders/paid` apontando para `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-webhooks`.

### 7. Smoke test

Ver [[Smoke-Test-Webhook-Shopify]]. Idealmente, fazer compra de teste real na loja conectada.

## ✔️ Validação final

- [ ] `shopify_connections` tem 1 linha pro tenant com `is_active=true`
- [ ] Tela "Integrações → Shopify" mostra ✅ conectado
- [ ] Webhooks listados em `shopify_webhooks` ≥ 2
- [ ] Smoke test 3/3 passou

## 🐞 Troubleshooting

### Sintoma: redirect callback falha com "invalid_state"
**Causa provável:** state cifrado expirou (>5min) ou usuário trocou de aba.
**Correção:** repetir o passo 3 (clicar "Conectar" de novo).

### Sintoma: callback retorna 401 "HMAC inválido"
**Causa provável:** `SHOPIFY_CLIENT_SECRET` no Supabase está desatualizado (ex: alguém clicou "Refresh" no Partners — ver [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]]).
**Correção:** copiar secret atual do Partners Dashboard e atualizar via [[Setar-Secret-EdgeFunction]].

### Sintoma: tenant não vê menu "Integrações"
**Causa provável:** user não tem role `supplier_admin`.
**Correção:** [[Promover-User-SuperAdmin]] (mesma lógica para `supplier_admin`).

### Sintoma: "Loja já conectada em outro tenant"
**Causa provável:** mesma loja foi conectada anteriormente em outra org (rara).
**Correção:** SQL: `UPDATE shopify_connections SET is_active=false WHERE shopify_domain = 'xxx.myshopify.com' AND org_id <> '<correto>';`

## 📞 Escalar para

- Se OAuth falhar repetidamente: Rian (`a.ordem.projeto@gmail.com`)
- Se for problema com a conta Shopify do tenant (ex: loja desabilitada): tenant via WhatsApp

## 📝 Histórico de execuções

| Data | Operador | Tenant | Resultado | Observações |
|---|---|---|---|---|
| 2026-05-XX | {{op}} | Ferstex | {{ok-falhou}} | {{notas}} |

## 🔗 Relacionado

- [[../05-Decisoes/006-shopify-readonly-scopes|ADR-006]]
- [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]]
- [[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]]
- [[Smoke-Test-Webhook-Shopify]]
- [[Setar-Secret-EdgeFunction]]
- [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]]
