---
title: "ADR-008 — Nameservers Hostinger nativos no lugar do Vercel-managed DNS"
tags:
  - adr
  - decisao
  - dns
  - infra
created: 2026-04-30
updated: 2026-05-01
status: accepted
numero: "008"
autor: Rian
---

# ADR-008 — Nameservers Hostinger nativos (`byte`/`pixel.dns-parking.com`) no lugar do Vercel-managed DNS

> [!success] Status: **accepted**

## 🌐 Contexto

Histórico do domínio `portalsistex.com`:

1. Registrado em Hostinger pela YNTHEGRA
2. Lovable configurou nameservers para `ns1.vercel-dns.com` / `ns2.vercel-dns.com` (Vercel-managed DNS)
3. Vercel-managed DNS estava na conta Vercel `synthegra` (Ferstex) — descoberto em 2026-04-30
4. Ao tentar mover o domínio para a conta Vercel da YNTHEGRA: `Vercel: This domain is linked to another Vercel account` — bloqueado pela origem (DNS authoritative)

Sair desse limbo só era possível se o DNS authoritative passasse a estar **fora** da conta Vercel original.

Forças:
- Independência da conta Vercel da Ferstex (princípio do [[005-infra-sempre-na-ynthegra|ADR-005]])
- DNS authoritative na YNTHEGRA, não em terceiros
- Custo: zero (Hostinger DNS gratuito incluído)

## 🎯 Decisão

**Nameservers `byte.dns-parking.com` e `pixel.dns-parking.com` (Hostinger nativos).** DNS authoritative passa a ser do Hostinger, gerenciado pela conta YNTHEGRA. Vercel passa a apontar via `A` e `CNAME` records configurados no Hostinger.

Records configurados:

| Tipo | Nome | Valor |
|---|---|---|
| A | `@` | `76.76.21.21` |
| CNAME | `www` | `cname.vercel-dns.com` |
| TXT | `_vercel` | `vc-domain-verify=portalsistex.com,...` |
| TXT | `_vercel.www` | `vc-domain-verify=www.portalsistex.com,...` |

## 🔀 Alternativas consideradas

### Alternativa A — Manter Vercel-managed DNS
- **Prós:** zero esforço, configuração automática Vercel.
- **Contras:** estava preso na conta Ferstex; mesmo se migrássemos seria conta Vercel novamente — terceirizamos authority.
- **Por que não escolhemos:** princípio de [[005-infra-sempre-na-ynthegra|ADR-005]] exige authority na YNTHEGRA.

### Alternativa B — Cloudflare DNS
- **Prós:** propagação rápida, dashboard moderno, free tier.
- **Contras:** mais um SaaS; Hostinger DNS já está incluso e funciona.
- **Por que não escolhemos:** sem benefício marginal pra justificar migração agora.

## 📈 Consequências

### Positivas
- DNS authoritative em conta YNTHEGRA
- Saímos do limbo "domain linked to another Vercel account"
- Independência: trocar de provider de hosting (Vercel → outro) não exige troca de nameserver

### Negativas / trade-offs aceitos
- **Hostinger DNS é lento** internamente (UI mostra novo, authoritative pode demorar 30-60min) — descoberto em produção, levou 37min na primeira propagação. Ver [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]].
- TXT verification do Vercel demora mais que o esperado por causa desse cache interno.

### Riscos
- **Risco:** Hostinger DNS cair → site fora do ar. **Mitigação:** baixíssima probabilidade; se acontecer, migrar pra Cloudflare DNS em < 30min (records já documentados).
- **Risco:** TTL alto cacheado em ISPs. **Mitigação:** TTL 300 nos records críticos.

## 🔁 Revisão

- **Quando revisar:** se Hostinger DNS apresentar problemas recorrentes OU se quisermos features que só Cloudflare oferece (ex: rate limiting na borda).
- **Como saberemos que a decisão foi errada:** propagação consistentemente > 1h ou downtime atribuível ao DNS.

## 🔗 Relacionado

- [[005-infra-sempre-na-ynthegra|ADR-005]] — princípio que motivou
- [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]] — propagação real observada
- [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]]
- [[../01-Contexto/Infraestrutura]] — DNS records atuais
