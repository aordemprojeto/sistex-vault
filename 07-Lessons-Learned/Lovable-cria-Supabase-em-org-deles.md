---
title: "Lesson — Lovable Cloud cria Supabase na ORG deles, não sua. Sair = perder acesso."
tags:
  - lesson-learned
  - lovable
  - supabase
  - estrategico
created: 2026-04-29
updated: 2026-05-01
status: ativo
severidade: critica
data-incidente: 2026-04-29
---

# 💡 Lesson Learned: Lovable Cloud cria Supabase na org **deles**, sair = perder acesso

> [!danger] Severidade: **crítica** (impacto existencial)
> Quando aconteceu: descoberto em **2026-04-29** durante migração; impacto evitado por sorte.

## 🐞 Problema enfrentado

**Sintoma:**
Ao tentar acessar o projeto Supabase original (`ngeigfbffuqsviruhrmc`) pela conta `a.ordem.projeto@gmail.com` no dashboard Supabase, **não aparecia na listagem de projetos**. Tampouco em qualquer outra conta YNTHEGRA.

**Impacto:**
- Risco de perder acesso permanente ao banco de produção quando saíssemos do Lovable
- Migration files locais existiam, mas dados de produção (configs, qualquer linha já existente) ficariam isolados na org Lovable
- Vendor lock-in invisível

**Como descobrimos:**
Investigação do dashboard Supabase em todas as contas que poderiam ser dono. Confirmado via Lovable support: Supabase pertence à **org Lovable Cloud**.

## 🔍 Causa raiz

> [!important] Lovable Cloud provisiona Supabase em conta interna deles, não na conta do cliente
> A "casa" do banco é a org Lovable. O cliente acessa via integração Lovable, não diretamente. **Sair do Lovable = perder o Supabase junto.**

**Por que aconteceu:**
- Modelo de negócio Lovable: oferece "Supabase grátis embutido" pra reduzir fricção do MVP
- Customer entende que "tem Supabase", mas o Supabase real está na conta da Lovable
- Não há aviso claro sobre essa amarração

**Por que não pegamos antes:**
- Lovable parecia ferramenta neutra de no-code; assumimos que provisionavam recursos no nome do cliente
- Documentação não destaca essa questão

## 🛠️ Solução aplicada

**Migração total para Supabase próprio na conta YNTHEGRA:**

1. Criado projeto Supabase novo (`eppxxuostbtkuuftgasp`) na conta `a.ordem.projeto@gmail.com`
2. 45 migrations aplicadas via `supabase db push`
3. 38 Edge Functions deployadas via `supabase functions deploy`
4. Secrets setados manualmente
5. Frontend atualizado para apontar pro novo Supabase
6. Smoke test passou com secret real

**Tempo para resolver:** ~4h de trabalho focado (sessão 2026-04-30 a 2026-05-01).

## 🛡️ Prevenção futura

- [x] Princípio formalizado em [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]: **toda infra crítica fica em conta YNTHEGRA**
- [x] Apagar projeto Supabase antigo Lovable (`ngeigfbffuqsviruhrmc`) — pendente, ver [[../01-Contexto/CLAUDE]] TODOs
- [ ] Em qualquer plataforma low-code/no-code futura, **ler termos sobre quem é dono dos recursos provisionados** antes de produção
- [ ] Documentar no [[../01-Contexto/Pessoas]] / contrato com sócios novos: nunca aceitar "Cloud embutido" sem confirmar ownership

## 📚 O que aprendemos

> **No-code "Cloud" é sempre conta de terceiros.** Lovable Cloud, Bubble, Webflow CMS, Notion — todos provisionam recursos em conta deles. Pra produção real, **migrar antes de virar refém**.
>
> **Vendor lock-in invisível** é o pior tipo: você acha que tem controle até precisar sair.

## 🔗 Relacionado

- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]] — princípio resultante
- [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]] — mesmo princípio aplicado a DNS
- [[Vercel-domain-limbo-quando-removido]] — sintoma análogo no Vercel
- Sessão: [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
