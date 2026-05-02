---
title: "Reunião 2026-04-28 — Alinhamento de prazo"
tags:
  - reuniao
  - ferstex
  - stenio
  - alinhamento
created: 2026-04-28
updated: 2026-05-01
data: 2026-04-28
duracao: "{{minutos-pendente}}"
formato: "{{presencial-online-pendente}}"
---

# 📅 Reunião — Alinhamento de prazo (Ferstex × YNTHEGRA)

> [!info] Metadados
> **Data:** 2026-04-28
> **Tema:** Alinhamento de expectativas e definição de prazo de demo

## 👥 Participantes

- **Stenio** — dono da Ferstex
- **Rian** — YNTHEGRA
- **Erick** — YNTHEGRA (presença a confirmar)

## 📋 Agenda

1. Status do projeto SISTEX (após 2 meses de desenvolvimento)
2. Expectativa de entrega
3. Definição de prazo concreto para demo

## 💬 Discussão

### Estado emocional do Stenio

> [!warning] Atenção registrada
> Stenio chegou à reunião **desanimado com a demora** (cerca de 2 meses entre acordo inicial e MVP). Ainda não havia visto o sistema funcionando com a loja dele.

### Realinhamento de expectativa

- Stenio **não pediu lançamento oficial** — quer ver o **fluxo principal funcionando** uma vez, com a loja Shopify dele de verdade.
- YNTHEGRA propôs **MVP demonstrável** focado no fluxo único:
  ```
  Compra Shopify → SISTEX → Etiqueta Melhor Envio → Motor de Produção
  ```
- Tudo que estiver fora desse fluxo único fica para depois da demo.

## ✅ Decisões tomadas

> [!success] Decisões
> - **Demo agendada para terça 2026-05-05** — responsável: Rian + Erick
> - **Escopo da demo:** apenas o fluxo único (compra → etiqueta → relatório de produção). Sem polishing, sem outras features.
> - **Stenio se compromete** a fornecer o domínio `xxx.myshopify.com` e reconectar Bling/Melhor Envio nos próximos dias.

## 🎯 Ações com responsável e prazo

| Ação | Responsável | Prazo | Status |
|---|---|---|---|
| Migrar infra para conta YNTHEGRA própria | Rian | 2026-05-01 | ✅ concluído |
| Criar app Shopify Partners (multi-tenant) | Rian | 2026-05-01 | ✅ concluído |
| Fornecer domínio `xxx.myshopify.com` da loja | Stenio | 2026-05-02 | ⏳ aberta |
| Reconectar Bling com credenciais Ferstex | Stenio | 2026-05-03 | ⏳ aberta |
| Reconectar Melhor Envio | Stenio | 2026-05-03 | ⏳ aberta |
| Cadastrar 5-10 produtos com SKU | YNTHEGRA + Stenio | 2026-05-04 | ⏳ aberta |
| Smoke test E2E manual | Rian | 2026-05-04 | ⏳ aberta |
| **Demo ao vivo** | Stenio + Rian + Erick | 2026-05-05 | ⏳ agendada |

## ❓ Questões em aberto

- Stenio reativará as integrações sozinho ou precisa de acompanhamento ao vivo?
- Validade do contrato R$60k vitalício — formalizar com advogado **antes ou depois** da demo?

## 📝 Notas livres

- Manter comunicação proativa com Stenio nos próximos dias — atualizações sucintas via WhatsApp.
- Não prometer features fora do fluxo único.
- Documentos premium (`status-sistex-ferstex.html`, `precificacao-sistex-v2.html`) já entregues a Stenio antes da reunião.

## 🔗 Relacionado

- [[../README|Tenant Ferstex]]
- Sprint resultante: [[../../../02-Sprints/MVP-Stenio-05mai]]
- Pricing: [[../../../10-Documentos-Premium/]]
- Pessoas: [[../../../01-Contexto/Pessoas]]
