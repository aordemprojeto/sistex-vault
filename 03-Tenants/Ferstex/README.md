---
title: Ferstex
tags:
  - tenant
  - supplier
  - piloto
created: 2026-05-01
updated: 2026-05-01
status: ativo
tipo: supplier
cnpj: "{{cnpj-pendente}}"
mrr: 2500
contrato: piloto
onboarded: 2026-04-28
org_id: 37c2fa01-adca-4320-84eb-65c46874126f
---

# 🏢 Ferstex

> [!info] Cliente piloto
> Primeiro tenant supplier da plataforma. Fabricante de moda fitness em Brusque/SC. Dono: **Stenio**. Demo crítica: **2026-05-05**.

## 📇 Identificação

- **Razão social:** Ferstex (completar)
- **Nome fantasia:** Ferstex
- **Tipo:** supplier (fornecedor)
- **CNPJ:** {{cnpj-pendente}}
- **Localização:** Brusque/SC
- **Site:** {{url-pendente}}
- **Org ID (DB):** `37c2fa01-adca-4320-84eb-65c46874126f`
- **Status DB:** approved

## 👤 Contato principal

Stenio — dono. Detalhes em [[Contato]].

## 💰 Comercial

- **Licença vitalícia:** R$60.000 (pagamento único)
- **Manutenção mensal:** R$2.500/mês
- **Split por pedido:** 70% YNTHEGRA / 30% Ferstex
- **MRR atual:** R$2.500
- **Início do contrato:** {{a-formalizar}}
- **Contrato formal:** ⏳ Pendente (com advogado SaaS)

Pricing completo: `precificacao-sistex-v2.html` em [[../../10-Documentos-Premium/]].

## 🔌 Integrações

Status detalhado em [[Integracoes]].

- ⏳ **Shopify** — aguardando domínio `xxx.myshopify.com` da loja
- ⏳ **Bling** — Stenio reconecta no painel admin Ferstex
- ⏳ **Melhor Envio** — Stenio reconecta no painel admin Ferstex
- 🔴 **Mercado Pago** — futuro
- 🔴 **Mercado Livre** — futuro
- 🔴 **Shopee** — futuro

## 📦 Catálogo

Detalhes em [[Catalogo-SKUs]] (placeholder).

- Total de produtos cadastrados: 0
- Mínimo para demo: 5-10 produtos com SKU casando com loja Shopify

## 📋 Status atual

> [!warning] Estado da relação em 2026-04-28
> Stenio estava **desanimado com a demora** (2 meses entre acordo e MVP). Reunião alinhou expectativa para **MVP demonstrável** (não lançamento). Demo agendada **2026-05-05**.

> [!important] Próximas ações com Stenio
> 1. Confirmar domínio `xxx.myshopify.com` da loja (bloqueia conexão)
> 2. Reconectar Bling com credenciais dele
> 3. Reconectar Melhor Envio com credenciais dele
> 4. Cadastrar produtos com SKU

## 📂 Documentos do tenant

- [[Contato]] — telefones, emails, redes
- [[Integracoes]] — status de cada conector
- [[Catalogo-SKUs]] — produtos e mapeamento SKU
- [[Reunioes/]] — atas com Stenio

## 🔗 Relacionado

- Sprint ativa: [[../../02-Sprints/MVP-Stenio-05mai]]
- Reunião que originou demo: [[Reunioes/2026-04-28-alinhamento-prazo]]
- Modelo comercial: [[../../01-Contexto/Negocio]]
- Stenio: [[../../01-Contexto/Pessoas]]

## 📝 Histórico

### 2026-04-28
- Reunião de alinhamento. Demo agendada para 2026-05-05.

### 2026-04-30
- Migração de infra para conta YNTHEGRA própria (Supabase + Vercel + DNS).

### 2026-05-01
- Org Ferstex criada no Supabase novo. User Rian inserido como `supplier_admin`.
- App Shopify Partners criado (multi-tenant).
