---
title: "Lesson — Vercel não permite 2 contas na mesma sessão de browser"
tags:
  - lesson-learned
  - vercel
  - browser
created: 2026-04-30
updated: 2026-05-01
status: ativo
severidade: baixa
data-incidente: 2026-04-30
---

# 💡 Lesson Learned: Vercel não permite 2 contas logadas na mesma sessão de browser

> [!danger] Severidade: **baixa** (atrito operacional, sem dano)
> Quando aconteceu: **2026-04-30**, durante migração entre conta `synthegra` (Ferstex) e nova conta YNTHEGRA.

## 🐞 Problema enfrentado

**Sintoma:**
Tentar abrir `vercel.com` em uma aba e logar com a outra conta automaticamente desloga da primeira (ou redireciona para a já logada). Sem opção de "switch account" robusta.

**Impacto:**
Operação de migração ficou desnecessariamente lenta — alternar entre as duas contas exigia logout/login a cada vez, perdendo state das telas abertas.

**Como descobrimos:**
Tentando abrir Vercel da org `synthegra` numa aba e Vercel da nova conta YNTHEGRA em outra. A segunda aba "roubava" a sessão.

## 🔍 Causa raiz

> [!important] Vercel usa cookie único por domínio
> Não suporta multi-account na mesma sessão de browser. Mesma limitação do Google Workspace antes do account-chooser.

**Por que aconteceu:**
- Cookie `_vercel_jwt` é único por host
- Vercel não tem fluxo "Add another account"

**Por que não pegamos antes:**
- Operação de migração entre contas é raríssima — não havíamos enfrentado.

## 🛠️ Solução aplicada

**Aba anônima (Incognito / InPrivate) para a segunda conta.**

- Aba normal → conta nova YNTHEGRA
- Aba anônima → conta antiga `synthegra` (Ferstex)
- Cada aba tem cookies independentes

Alternativa: perfis de browser separados (Chrome Profiles).

**Tempo para resolver:** imediato após perceber.

## 🛡️ Prevenção futura

- [x] Documentar em runbook [[../06-Runbooks/Migrar-Dominio-Para-Vercel]] (passo 1: abrir aba anônima)
- [ ] Considerar criar perfis Chrome dedicados ("YNTHEGRA-prod" / "Cliente-X-legacy") para operações administrativas

## 📚 O que aprendemos

> Quando precisar operar duas contas Vercel (ou outros SaaS com mesma limitação), abrir aba anônima é mais rápido que logout/login. Vale para Supabase Dashboard, Shopify Partners, Hostinger, etc.

## 🔗 Relacionado

- [[Vercel-domain-limbo-quando-removido]]
- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]
- [[../06-Runbooks/Migrar-Dominio-Para-Vercel]]
- Sessão: [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
