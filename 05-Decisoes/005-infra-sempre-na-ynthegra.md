---
title: "ADR-005 — Toda infraestrutura crítica fica em conta YNTHEGRA"
tags:
  - adr
  - decisao
  - estrategico
  - infra
  - critico
created: 2026-04-30
updated: 2026-05-01
status: accepted
numero: "005"
autor: Rian
---

# ADR-005 — Toda infraestrutura crítica fica em conta YNTHEGRA

> [!danger] Status: **accepted** · Severidade estratégica máxima

## 🌐 Contexto

Em 2026-04-30, durante migração técnica, descobrimos:

- **Supabase de produção** (`ngeigfbffuqsviruhrmc`) estava em **org Lovable** — nem o Rian conseguia acessar pelo dashboard.
- **Vercel** estava em conta `grupoferstex@gmail.com` (org `synthegra`) — email da Ferstex (cliente), **não** da YNTHEGRA.
- **DNS Vercel-managed** estava nessa mesma conta Vercel da Ferstex.

Resultado: a YNTHEGRA não tinha controle real da própria infra do produto SISTEX. Se Stenio resolvesse "puxar o tapete", poderia bloquear acesso ao Supabase/Vercel/DNS.

Forças:
- YNTHEGRA é dona do produto e relação comercial
- Tenant não pode ter capacidade técnica de bloquear plataforma
- Custo de migração é alto, mas custo de não migrar é existencial

## 🎯 Decisão

**REGRA INVIOLÁVEL:** toda conta de infraestrutura crítica é da YNTHEGRA, não de fornecedores ou drops.

| Recurso | Conta correta |
|---|---|
| Supabase | `a.ordem.projeto@gmail.com` (Org YNTHEGRA.Org) |
| Vercel | `aordemprojeto-5068` (criada 2026-05-01) |
| GitHub | Org `YNTHEGRA` |
| Hostinger (domínio) | YNTHEGRA |
| Shopify Partners | Org YNTHEGRA / A ORDEM |
| 1Password | YNTHEGRA |

**Se algum dia aparecer infra em conta de fornecedor/drop, é BUG ESTRATÉGICO — migrar imediatamente.**

## 🔀 Alternativas consideradas

### Alternativa A — Manter infra distribuída entre contas
- **Prós:** zero esforço de migração.
- **Contras:** YNTHEGRA dependente politicamente do tenant.
- **Por que não escolhemos:** existencial.

### Alternativa B — Migrar parcialmente (só Supabase, deixar Vercel)
- **Prós:** menos esforço.
- **Contras:** ainda há ponto de bloqueio (DNS/Vercel).
- **Por que não escolhemos:** uma única dependência crítica fora de controle quebra a regra.

## 📈 Consequências

### Positivas
- YNTHEGRA controla 100% da infra do produto
- Pode adicionar/remover qualquer tenant sem comprometer plataforma
- Independência política: relação com Stenio passa a ser puramente comercial

### Negativas / trade-offs aceitos
- Migração custou ~5h de trabalho focado (sessão 2026-04-30 → 2026-05-01)
- 37min de propagação DNS no momento crítico ([[../07-Lessons-Learned/DNS-Hostinger-cache-37min]])
- Conta Vercel atual é **conta pessoal Hobby** do Rian — pendência: migrar para Team paga em org YNTHEGRA dedicada

### Riscos
- **Risco:** Rian perder acesso à conta `a.ordem.projeto@gmail.com`. **Mitigação:** 2FA, recovery codes salvos, replicar acesso para Erick.
- **Risco:** conta Hobby Vercel atinge limites Free. **Mitigação:** monitorar e migrar para Team paga antes de saturar.

## 🔁 Revisão

- **Quando revisar:** quando MRR justificar Vercel Team paga (~US$20/mês).
- **Como saberemos que a decisão foi errada:** nunca. É princípio.

## 🔗 Relacionado

- [[../07-Lessons-Learned/Vercel-multi-account-mesma-sessao-impossivel]]
- [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]]
- [[../07-Lessons-Learned/Lovable-cria-Supabase-em-org-deles]]
- [[008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]
- [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
