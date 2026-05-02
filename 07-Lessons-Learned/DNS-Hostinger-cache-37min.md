---
title: "Lesson — DNS Hostinger demora 37min mesmo com TTL 300"
tags:
  - lesson-learned
  - dns
  - hostinger
created: 2026-04-30
updated: 2026-05-01
status: ativo
severidade: media
data-incidente: 2026-04-30
---

# 💡 Lesson Learned: DNS Hostinger demora 37 minutos mesmo com TTL 300

> [!danger] Severidade: **média**
> Quando aconteceu: **2026-04-30**, durante migração para conta Vercel YNTHEGRA.

## 🐞 Problema enfrentado

**Sintoma:**
Após adicionar TXT records no Hostinger (`_vercel` e `_vercel.www`) com TTL 300, o Vercel continuava mostrando "Verification Needed" por mais de 30 minutos. Lookups via `Resolve-DnsName ... -Server 1.1.1.1` retornavam o valor antigo (ou vazio).

**Impacto:**
Bloqueou conclusão da migração de domínio para Vercel. Demo do Stenio iminente. Ansiedade de "será que algo está errado nos records?" levou a re-checagens repetidas.

**Como descobrimos:**
Lookups DNS em paralelo no `byte.dns-parking.com` (authoritative) **vs.** Cloudflare 1.1.1.1 mostraram que o **próprio authoritative do Hostinger** estava servindo valor desatualizado, não era cache de resolver.

## 🔍 Causa raiz

> [!important] Hostinger DNS tem cache interno entre UI e authoritative
> A UI do Hostinger atualiza imediatamente, mas a propagação para os nameservers (`byte.dns-parking.com` / `pixel.dns-parking.com`) leva tempo significativo — observado **37 minutos** na primeira aplicação, mesmo com TTL 300.

**Por que aconteceu:**
- Hostinger DNS não é "edge-fresh" como Cloudflare ou Route53
- Mudança no painel → fila interna → propagação para servidores authoritative
- TTL no record só afeta cache de resolver downstream, não o tempo até o authoritative servir o novo valor

**Por que não pegamos antes:**
- Primeira vez configurando Hostinger DNS pra Vercel
- Documentação do Hostinger não enfatiza essa latência
- Esperávamos comportamento similar a Cloudflare (segundos)

## 🛠️ Solução aplicada

**Esperar.** Após 37 minutos, os TXT records começaram a propagar e o Vercel validou ownership.

**Comandos de diagnóstico usados:**

```powershell
# Checar nameservers authoritative
Resolve-DnsName -Name "portalsistex.com" -Type NS -Server 1.1.1.1

# Checar TXT direto no authoritative do Hostinger
Resolve-DnsName -Name "_vercel.portalsistex.com" -Type TXT -Server byte.dns-parking.com

# Limpar cache local
ipconfig /flushdns
```

**Tempo para resolver:** ~40 minutos (esperando propagação)

## 🛡️ Prevenção futura

- [x] Documentar latência do Hostinger DNS em [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]
- [ ] Em próximas mudanças DNS, **aplicar com 1h+ de antecedência** quando houver dependência crítica
- [ ] Avaliar migração para Cloudflare DNS se latência virar atrito recorrente
- [x] Saber checar authoritative diretamente, não só via resolver: `Resolve-DnsName ... -Server byte.dns-parking.com`

## 📚 O que aprendemos

> Cache interno entre painel e authoritative existe e é invisível. **TTL no record não controla isso.** Para DNS providers menores, esperar é a única ação correta — checar authoritative diretamente confirma se o problema é local ou no provider.

> **Cloudflare 1.1.1.1** respeita TTL agressivamente (cacheia até o final). **Google 8.8.8.8 e Quad9 9.9.9.9** atualizam mais rápido. Useful pra confirmar propagação quando o resolver default está cacheando.

## 🔗 Relacionado

- [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]
- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]
- [[Vercel-domain-limbo-quando-removido]]
- Sessão: [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
