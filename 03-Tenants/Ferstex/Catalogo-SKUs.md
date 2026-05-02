---
title: Ferstex — Catálogo e SKUs
tags:
  - tenant
  - ferstex
  - catalogo
  - sku
created: 2026-05-01
updated: 2026-05-01
status: vazio
---

# 📦 Ferstex — Catálogo e SKUs

> [!warning] Placeholder
> Catálogo Ferstex ainda não foi cadastrado no SISTEX. Mínimo para a demo: **5-10 produtos com SKU casando com a loja Shopify do Stenio**.

## Estado atual

| Métrica | Valor |
|---|---|
| Produtos cadastrados | 0 |
| Variantes | 0 |
| Mínimo para demo | 5-10 |

## Lógica de matching SKU

O SKU Matching usa cascade de 6 níveis (ver [[../../01-Contexto/Arquitetura]] seção Features). A ordem é:

1. SKU exato Ferstex ↔ SKU loja Shopify
2. EAN/GTIN
3. Código interno legacy
4. Slug normalizado
5. Match parcial por nome+variante
6. Fallback manual

> [!info] Para o MVP, basta nível 1
> Cadastrar produtos com **SKU idêntico ao da loja Shopify do Stenio**. Os outros níveis são otimizações posteriores.

## Tabela de produtos (a preencher)

| SKU Ferstex | Nome | Variante | EAN | SKU Shopify casado | Status |
|---|---|---|---|---|---|
| {{sku-1}} | {{nome}} | {{cor-tamanho}} | {{ean}} | {{sku-loja}} | pendente |

## Fonte dos dados

- Catálogo Ferstex físico/sistema interno (Bling) — Stenio fornecerá
- SKUs da loja Shopify — extraídos via API após conexão

## Próximos passos

- [ ] Stenio fornecer planilha com SKU/nome/variante/EAN dos produtos da loja
- [ ] Importar via SQL (ou cadastro manual no painel)
- [ ] Validar matching com 1-2 SKUs de teste
- [ ] Smoke test E2E: comprar produto X na loja → confirmar pedido aparece com produto Ferstex correto

## 🔗 Relacionado

- [[README|Tenant Ferstex]]
- [[Integracoes]] — Shopify precisa estar conectado primeiro
- [[../../02-Sprints/MVP-Stenio-05mai]] — sprint que depende disso
