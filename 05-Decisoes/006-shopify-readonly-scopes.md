---
title: "ADR-006 — App Shopify com escopos read-only (read_orders, read_products)"
tags:
  - adr
  - decisao
  - shopify
  - seguranca
created: 2026-04-30
updated: 2026-05-01
status: accepted
numero: "006"
autor: Rian
---

# ADR-006 — App Shopify com escopos read-only (`read_orders`, `read_products`)

> [!success] Status: **accepted**

## 🌐 Contexto

App Shopify "Sistex" precisa ler pedidos e produtos da loja do tenant para alimentar o fluxo SISTEX (SKU matching, Motor de Produção). Shopify oferece escopos `read_*` e `write_*` para cada recurso.

Forças:
- Princípio de menor privilégio
- App público requer revisão Shopify proporcional aos escopos pedidos (mais escopos = revisão mais demorada)
- SISTEX **não escreve** na loja Shopify do tenant — apenas consome dados

## 🎯 Decisão

**App Shopify Sistex usa apenas escopos read-only:**

```
read_orders, read_products
```

**Nunca** pedir `write_orders`, `write_products`, ou qualquer escopo que permita modificar a loja do tenant.

API webhooks version: `2026-04`.

## 🔀 Alternativas consideradas

### Alternativa A — Adicionar `write_orders` para sincronizar status
- **Como funcionaria:** SISTEX atualizaria pedidos no Shopify (ex: marcar como "fulfilled" quando etiqueta gerada).
- **Prós:** experiência integrada para o cliente final do tenant.
- **Contras:** acesso de escrita na loja é privilégio crítico; revisão Shopify mais lenta; risco de bug do SISTEX corromper dados do tenant.
- **Por que não escolhemos:** fora do escopo MVP. Quando precisarmos, pedimos em ADR separada com mitigações.

### Alternativa B — `read_all_orders` (incluir pedidos > 60 dias)
- **Prós:** acesso histórico completo.
- **Contras:** Shopify exige justificativa adicional na revisão; SISTEX só precisa do fluxo "going forward".
- **Por que não escolhemos:** desnecessário pra demo e MVP.

## 📈 Consequências

### Positivas
- Princípio de menor privilégio respeitado
- Revisão Shopify mais rápida (escopos mínimos)
- Argumento de venda: "SISTEX não toca na sua loja, apenas lê"

### Negativas / trade-offs aceitos
- Não conseguimos atualizar status dos pedidos no Shopify (cliente final do tenant verá divergência)
- Sincronização de inventário Shopify ↔ Bling fica unidirecional

### Riscos
- **Risco:** tenant pedir feature que requer escrita. **Mitigação:** ADR nova, escopos incrementais com novo OAuth flow.

## 🔁 Revisão

- **Quando revisar:** quando tenant pedir feature que exige escrita E o trade-off de segurança for aceitável.
- **Como saberemos que a decisão foi errada:** nunca — princípio de menor privilégio é regra geral.

## 🔗 Relacionado

- [[007-app-shopify-public-custom-distribution|ADR-007]] — distribuição do app
- [[../03-Tenants/Ferstex/Integracoes]]
- [[../01-Contexto/Infraestrutura]] — credenciais do app Shopify
