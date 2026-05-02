---
title: "ADR-007 — App Shopify Public com Custom Distribution (multi-loja sem revisão)"
tags:
  - adr
  - decisao
  - shopify
  - distribuicao
created: 2026-04-30
updated: 2026-05-01
status: accepted
numero: "007"
autor: Rian
---

# ADR-007 — App Shopify Public com Custom Distribution

> [!success] Status: **accepted**

## 🌐 Contexto

Shopify oferece dois tipos de app:

| Tipo | Características |
|---|---|
| **Custom app** | Privado, restrito a UMA loja específica. Não passa por revisão. |
| **Public app** | Pode instalar em N lojas. Para listar na App Store exige revisão Shopify. **Custom Distribution** permite N lojas sem revisão (URL OAuth direta). |

SISTEX vai conectar **múltiplas lojas** (Ferstex hoje, futuros suppliers e drops) — Custom App não serve. Mas não precisamos estar na App Store ainda.

Forças:
- Multi-loja é requisito (Ferstex + futuros)
- Tempo curto até demo: revisão Shopify pode levar dias/semanas
- Custom Distribution = OAuth direto via URL, sem listing público

## 🎯 Decisão

**App Shopify Sistex configurado como Public com Custom Distribution.**

Cada tenant nosso recebe link de instalação direto (OAuth URL) que aponta pro app, sem passar pela App Store. Quando o produto amadurecer e tivermos volume, **migramos para listing público com revisão Shopify**.

## 🔀 Alternativas consideradas

### Alternativa A — Custom App (uma instalação por tenant)
- **Como funcionaria:** criar Custom App separado para cada tenant.
- **Prós:** zero burocracia.
- **Contras:** N apps a manter, secrets diferentes por loja, código fica condicional.
- **Por que não escolhemos:** não escala.

### Alternativa B — Public App listado na App Store
- **Prós:** descoberta orgânica, credibilidade.
- **Contras:** revisão Shopify (semanas), exige policies, ToS, screenshots, tradução, etc.
- **Por que não escolhemos:** não temos esse tempo até a demo de 2026-05-05. Migrar quando tiver maturidade.

### Alternativa C — Embedded App
- **Prós:** UX dentro do admin Shopify.
- **Contras:** SISTEX é portal próprio (`portalsistex.com`); não faz sentido embeddar.
- **Por que não escolhemos:** não combina com modelo do produto.

## 📈 Consequências

### Positivas
- Multi-loja sem revisão Shopify
- Mesma codebase serve N tenants
- Onboarding de tenant novo é só gerar link OAuth

### Negativas / trade-offs aceitos
- Não aparece em busca pública na App Store (mas YNTHEGRA capta clientes via venda direta, não via Shopify)
- Algumas features Shopify exigem listing (ex: aparecer em recomendações Shopify) — não usamos

### Riscos
- **Risco:** Shopify mudar política e exigir review para Custom Distribution. **Mitigação:** monitorar release notes; se mudar, fazer revisão.

## 🔁 Revisão

- **Quando revisar:** quando tivermos 10+ tenants conectados e a App Store fizer sentido como canal.
- **Como saberemos que a decisão foi errada:** Shopify rejeitar instalação OU competidores aparecerem na App Store roubando deals.

## 🔗 Relacionado

- [[006-shopify-readonly-scopes|ADR-006]] — escopos
- [[../01-Contexto/Infraestrutura]] — Client ID/URL do app
- [[../03-Tenants/Ferstex/Integracoes]]
- [[../06-Runbooks/Conectar-Shopify-Tenant]]
