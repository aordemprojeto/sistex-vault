---
title: "Lesson — Domínio Vercel fica em limbo (DEPLOYMENT_NOT_FOUND) quando removido"
tags:
  - lesson-learned
  - vercel
  - dns
  - dominio
created: 2026-04-30
updated: 2026-05-01
status: ativo
severidade: media
data-incidente: 2026-04-30
---

# 💡 Lesson Learned: Domínio Vercel fica em limbo "DEPLOYMENT_NOT_FOUND" quando removido

> [!danger] Severidade: **média**
> Quando aconteceu: **2026-04-30**, durante migração de Vercel `synthegra` → conta YNTHEGRA.

## 🐞 Problema enfrentado

**Sintoma:**
Após remover `portalsistex.com` do projeto Vercel antigo (na org `synthegra`/Ferstex), o domínio:
- Não conseguia ser adicionado à conta nova YNTHEGRA: `Vercel: This domain is linked to another Vercel account`
- Acessar diretamente o domínio mostrava `DEPLOYMENT_NOT_FOUND`

**Impacto:**
Site ficou off (404 Vercel) durante o intervalo entre remoção da conta antiga e adição na conta nova. Tempo crítico de migração.

**Como descobrimos:**
Tentamos adicionar o domínio na conta nova imediatamente após remover da antiga e batemos na mensagem de erro.

## 🔍 Causa raiz

> [!important] Domínio fica "linked" ao último deploy mesmo após remoção do projeto
> Vercel mantém referência interna do domínio à conta original até propagar a remoção. **Trocar nameservers para fora do Vercel-managed DNS desbloqueia.**

**Por que aconteceu:**
- DNS authoritative ainda era Vercel-managed (`ns1/ns2.vercel-dns.com`) na conta antiga
- Vercel detecta que o nameserver pertence à infra Vercel e bloqueia adição em outra conta

**Por que não pegamos antes:**
- Cenário inédito (migração entre contas Vercel é raro)

## 🛠️ Solução aplicada

1. **Trocar nameservers no Hostinger** de `ns1/ns2.vercel-dns.com` para Hostinger nativos (`byte/pixel.dns-parking.com`).
2. Aguardar propagação NS (~10min, mais rápido que TXT pelo que observamos).
3. Re-adicionar `portalsistex.com` na conta Vercel YNTHEGRA — passou.
4. Configurar A/CNAME/TXT records no Hostinger pointing para Vercel ([[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]).

**Tempo para resolver:** ~50 minutos (somando troca NS + propagação inicial + re-add + 37min de cache TXT pra verification).

## 🛡️ Prevenção futura

- [x] [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]] formaliza preferência por DNS authoritative na YNTHEGRA, fora do Vercel
- [x] Runbook [[../06-Runbooks/Migrar-Dominio-Para-Vercel]] documenta passos
- [ ] Em qualquer setup novo, **NUNCA usar Vercel-managed DNS** — sempre Hostinger ou Cloudflare

## 📚 O que aprendemos

> "Domain linked to another Vercel account" não é só sobre login — é sobre **quem controla o nameserver authoritative**. Se for Vercel, mesmo removendo o domínio da conta antiga, a "amarração" persiste.
>
> **DNS authoritative na YNTHEGRA = liberdade pra mover entre providers de hosting** sem ficar refém.

## 🔗 Relacionado

- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]
- [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]
- [[DNS-Hostinger-cache-37min]]
- [[Vercel-multi-account-mesma-sessao-impossivel]]
- [[../06-Runbooks/Migrar-Dominio-Para-Vercel]]
