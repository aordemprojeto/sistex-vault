---
title: "ADR-003 — Tokens OAuth criptografados com AES-GCM v2 + HKDF-SHA256"
tags:
  - adr
  - decisao
  - seguranca
  - criptografia
  - oauth
created: 2026-04-22
updated: 2026-05-01
status: accepted
numero: "003"
autor: Rian
---

# ADR-003 — Tokens OAuth criptografados com AES-GCM v2 + HKDF-SHA256

> [!success] Status: **accepted**

## 🌐 Contexto

SISTEX armazena tokens OAuth (access_token, refresh_token) de Bling, Shopify, Mercado Pago, Mercado Livre, Shopee e Melhor Envio. Esses tokens dão acesso a sistemas terceiros do tenant — vazamento é incidente crítico.

Versão inicial (v1) usava AES-GCM com chave derivada por padding zero da `ENCRYPTION_MASTER_KEY` — funcional mas sem KDF apropriada. Auditoria de hardening (Bloco 4) recomendou KDF dedicada.

Forças:
- Defesa em profundidade: mesmo com dump do banco, atacante não lê tokens sem `ENCRYPTION_MASTER_KEY`
- Migração não pode quebrar tokens já cifrados (v1 em produção)
- Performance aceitável (cifragem por linha, não em massa)

## 🎯 Decisão

**Adotamos AES-GCM v2 com chave derivada via HKDF-SHA256, salt fixo.** Formato:

```
v2:<base64(IV(12) ‖ ciphertext+tag(16))>
```

- **`encryptValue` SEMPRE produz v2.**
- **Leitura aceita v1 e v2** (fallback transparente).
- **Migração lazy:** linha v1 lida com sucesso é re-encriptada como v2 e atualiza `crypto_version`.

Helper: `supabase/functions/_shared/encryption.ts`.

## 🔀 Alternativas consideradas

### Alternativa A — Continuar com v1 (padding zero)
- **Prós:** sem migração.
- **Contras:** KDF inadequada; auditoria já apontou; chave única para todas as cifragens (sem `info` HKDF).
- **Por que não escolhemos:** dívida técnica de segurança.

### Alternativa B — KMS externo (Supabase Vault, AWS KMS)
- **Prós:** chave nunca sai do KMS, melhor compliance.
- **Contras:** custo, complexidade, latência por request, dependência externa adicional.
- **Por que não escolhemos:** overkill para fase MVP; HKDF + master key em secret é suficiente até chegarmos a regulado.

### Alternativa C — Migração eager (re-encrypt todos de uma vez)
- **Prós:** v1 some do banco rapidamente.
- **Contras:** lock pesado; risco de migration falhar no meio.
- **Por que não escolhemos:** lazy é mais seguro e converge naturalmente.

## 📈 Consequências

### Positivas
- KDF moderna (HKDF-SHA256) com salt fixo — chave de cifragem ≠ master key
- Backward-compat: v1 ainda lida sem incidente
- Migração progressiva conforme tokens são acessados

### Negativas / trade-offs aceitos
- Coexistência de v1 e v2 enquanto não há sweep
- Trocar `ENCRYPTION_MASTER_KEY` invalida TUDO — precisa re-cifragem em massa antes
- Salt fixo (não rotacionado) — adequado para MVP, revisitar em escala

### Riscos
- **Risco:** perder `ENCRYPTION_MASTER_KEY` = perder acesso a todos os tokens. **Mitigação:** salvo em 1Password, replicado para Erick em momento futuro.
- **Risco:** trocar a master key sem migração defensiva. **Mitigação:** documentado em [[../01-Contexto/CLAUDE]] regra "NUNCA".

## 🔁 Revisão

- **Quando revisar:** quando atingirmos 1000+ tenants OU quando exigência regulatória (LGPD enterprise) demandar KMS.
- **Como saberemos que a decisão foi errada:** vazamento que rastreie a v1; ou auditoria externa apontar HKDF com salt fixo como inaceitável.

## 🔗 Relacionado

- [[002-rls-em-todas-tabelas|ADR-002]] — outra camada de defesa
- [[../01-Contexto/Arquitetura]] — seção de segurança
- Helper: `supabase/functions/_shared/encryption.ts`
