---
title: Negocio
tags:
  - contexto
  - negocio
created: 2026-05-01
updated: 2026-05-01
status: active
---

# 💼 Negócio — SISTEX/YNTHEGRA

## O que é YNTHEGRA?

**YNTHEGRA** é a empresa-plataforma. É a "casa" onde mora a infraestrutura de software (Supabase, Vercel, Stripe, GitHub, domínios). YNTHEGRA é dona do produto SISTEX.

## O que é SISTEX?

**SISTEX** é uma plataforma SaaS multi-tenant para **dropshipping de moda fitness**. Conecta fornecedores (que produzem) a revendedores (que vendem online) de forma automatizada.

## Modelo de tenants

| Tipo | Quem é | Paga |
|---|---|---|
| `supplier` (fornecedor) | Ferstex (primeiro), futuros suppliers | R$60k vitalício + R$2.5k/mês |
| `reseller` (revendedor / drop) | Lojistas que revendem o catálogo | R$130/mês (trial 10 dias) |
| `admin` | YNTHEGRA — opera a plataforma | — |

## Modelo comercial atual (acordado com Stenio)

> [!info] Acordo Ferstex × YNTHEGRA
> - **Licença vitalícia para Ferstex:** R$60.000 (pagamento único)
> - **Manutenção mensal Ferstex:** R$2.500/mês
> - **Drops/revendedores:** R$130/mês cada (trial 10 dias) — receita recorrente da YNTHEGRA
> - **Split por pedido:** 70% YNTHEGRA / 30% fornecedor (editável por tenant)

Plano comercial completo: ver `precificacao-sistex-v2.html` em [[../10-Documentos-Premium/]].

## 🔥 Dor #1 que o SISTEX resolve

**Relatório consolidado de produção** (Motor de Produção). Antes do SISTEX, fornecedores tipo Ferstex precisavam compilar manualmente os pedidos de várias plataformas (Shopify, ML, Shopee) num único relatório de produção da fábrica. SISTEX automatiza isso.

> [!important] Esse é o motor de retenção
> Sem o Motor de Produção, drops cancelam e fornecedores abandonam. É a feature âncora.

## Estratégia de retenção

SISTEX precisa virar **infraestrutura do negócio**, não ferramenta opcional. Quanto mais integrações (Bling, Melhor Envio, marketplaces) o tenant ativa pelo SISTEX, mais doloroso fica sair. Cada integração ativada é um "lock-in" positivo.

## Quem é a Ferstex?

Fabricante de moda fitness em **Brusque/SC**. **Dono: Stenio.** É o **primeiro tenant supplier** e parceiro-piloto da YNTHEGRA. O catálogo Ferstex é o primeiro a estar disponível pra revenda na plataforma.

Detalhes do tenant: [[../03-Tenants/Ferstex/README]].

## 🔗 Relacionado

- [[Arquitetura]] — como o produto é construído
- [[Pessoas]] — quem opera
- [[Infraestrutura]] — onde mora
- [[../05-Decisoes/]] — decisões estratégicas
