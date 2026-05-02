---
title: "ADR-004 — Replay protection em webhooks com janela de 10 minutos"
tags:
  - adr
  - decisao
  - seguranca
  - webhooks
created: 2026-04-23
updated: 2026-05-01
status: accepted
numero: "004"
autor: Rian
---

# ADR-004 — Replay protection em webhooks com janela de 10 minutos

> [!success] Status: **accepted**

## 🌐 Contexto

Webhooks recebem dados de Shopify, Bling, Melhor Envio, Shopee, Mercado Pago. Mesmo com [HMAC validado](../01-Contexto/Arquitetura.md) (segurança da origem), atacante que capture um webhook válido pode **reenviá-lo** para causar duplicação de pedidos, débito errado de carteira, etc.

Forças:
- Atrasos de rede legítimos (até alguns minutos em casos extremos)
- Diferenças de relógio entre servidores (NTP drift)
- Não queremos rejeitar webhooks legítimos por janela apertada demais

## 🎯 Decisão

**Janela de replay de 600 segundos (10 minutos)** em todos os webhooks. Helper centralizado em `_shared/replay_protection.ts`. Aceita timestamp em segundos, ms ou ISO-8601 conforme a plataforma.

Headers detectados por plataforma:

| Plataforma | Header | Formato |
|---|---|---|
| Shopify | `X-Shopify-Webhook-Triggered-At` | ISO-8601 |
| Bling | `X-Bling-Event-Timestamp` | epoch (s) |
| Melhor Envio | `X-MelhorEnvio-Timestamp` | epoch |
| Shopee | `X-Shopee-Timestamp` | epoch |
| Mercado Pago | extrai `ts=...` do header `X-Signature` | epoch |

Em modo dev (sem secret), apenas loga warning. Em prod, rejeita 401.

## 🔀 Alternativas consideradas

### Alternativa A — Sem replay protection
- **Prós:** simples.
- **Contras:** vulnerabilidade conhecida; HMAC sozinho não impede replay.
- **Por que não escolhemos:** baseline de segurança inaceitável.

### Alternativa B — Janela 60s
- **Prós:** janela de ataque mínima.
- **Contras:** rejeita webhooks legítimos com atraso de rede ou retry; falsos positivos altos.
- **Por que não escolhemos:** experiência ruim com plataformas que retentam após 30-90s.

### Alternativa C — Janela 60min
- **Prós:** zero falso positivo.
- **Contras:** janela ampla demais para um atacante operar.
- **Por que não escolhemos:** 10min é o ponto de equilíbrio adotado pela indústria (Stripe, Shopify, GitHub).

### Alternativa D — Nonce tracking (idempotency real)
- **Prós:** impede 100% dos replays.
- **Contras:** exige tabela com TTL, custo de storage; algumas plataformas não enviam nonce único.
- **Por que não escolhemos:** janela 10min + idempotency key da própria plataforma (event_id) já cobre 99% dos casos. Pode evoluir.

## 📈 Consequências

### Positivas
- Defesa contra replay sem complicar arquitetura
- Compatível com retries legítimos das plataformas
- Helper único = comportamento consistente

### Negativas / trade-offs aceitos
- Webhook genuíno com atraso > 10min é rejeitado (raro mas possível)
- Não impede atacante que replay no primeiro 1-2min após captura

### Riscos
- **Risco:** clock drift do servidor. **Mitigação:** Supabase Edge Functions usa NTP sincronizado.
- **Risco:** atacante captura e replay imediato. **Mitigação:** combina com idempotency em pagamentos (ver `purchase_orders.idempotency_key`).

## 🔁 Revisão

- **Quando revisar:** se observarmos rejeições legítimas em log (`replay_window_exceeded`).
- **Como saberemos que a decisão foi errada:** logs com falso-positivo recorrente OU incidente de replay bem-sucedido.

## 🔗 Relacionado

- [[../01-Contexto/Arquitetura]] — padrões de segurança
- Helper: `supabase/functions/_shared/replay_protection.ts`
