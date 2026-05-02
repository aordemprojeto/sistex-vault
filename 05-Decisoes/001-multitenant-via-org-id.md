---
title: "ADR-001 — Multi-tenant via org_id em todas as tabelas operacionais"
tags:
  - adr
  - decisao
  - multitenant
  - postgres
created: 2026-04-22
updated: 2026-05-01
status: accepted
numero: "001"
autor: Rian
---

# ADR-001 — Multi-tenant via `org_id` em todas as tabelas operacionais

> [!success] Status: **accepted**

## 🌐 Contexto

SISTEX serve múltiplos tenants distintos (suppliers, resellers, admin YNTHEGRA) num único banco Postgres. Precisamos isolar dados entre tenants sem complicar queries nem provisionar bancos separados.

Forças em jogo:
- Operação simples (1 banco, 1 deploy)
- Custo baixo (Free tier do Supabase)
- RLS do Postgres permite isolamento por linha de forma nativa
- Necessidade de cross-tenant para super_admin YNTHEGRA

## 🎯 Decisão

**Toda tabela operacional tem coluna `org_id` (UUID, FK para `public.organizations.id`).** A pertinência de uma linha a um tenant é determinada exclusivamente por essa FK.

Combinada com [[002-rls-em-todas-tabelas|ADR-002]] (RLS em todas as tabelas), garante isolamento real.

## 🔀 Alternativas consideradas

### Alternativa A — Banco por tenant
- **Como funcionaria:** cada tenant em projeto Supabase separado.
- **Prós:** isolamento absoluto, sem risco de query leak.
- **Contras:** custo escalando linearmente (Supabase cobra por projeto), operação 10x mais complexa, migrations N vezes, super_admin teria que conectar em N projetos pra ver dashboard cross-tenant.
- **Por que não escolhemos:** custo e complexidade incompatíveis com fase MVP.

### Alternativa B — Schema por tenant (mesmo banco)
- **Como funcionaria:** `CREATE SCHEMA tenant_<id>` por tenant; queries dinâmicas selecionam schema.
- **Prós:** isolamento no nível de schema.
- **Contras:** Postgres tem limite prático em milhares de schemas; migrations precisam aplicar em todos; cross-tenant exige UNION; PostgREST/Supabase não lida nativamente.
- **Por que não escolhemos:** modelo não escala para milhares de drops + suppliers.

### Alternativa C — Sem `org_id`, isolamento só no app
- **Prós:** nenhum.
- **Contras:** qualquer bug de filtro vaza dados entre tenants.
- **Por que não escolhemos:** segurança crítica delegada à camada errada.

## 📈 Consequências

### Positivas
- Migrations aplicam uma vez para todos os tenants
- Cross-tenant para super_admin é trivial
- RLS Postgres faz cumprimento no nível mais baixo possível
- Custo plano (Free tier serve qualquer número de tenants até saturar IO/storage)

### Negativas / trade-offs aceitos
- Erro de policy RLS pode vazar dados — exige revisão criteriosa
- Toda nova tabela precisa lembrar de adicionar `org_id` + policy ([[../06-Runbooks/]] reforça)
- Backups são sempre globais (não dá pra restaurar 1 tenant sem afetar outros)

### Riscos
- **Risco:** RLS desabilitada acidentalmente → vazamento. **Mitigação:** lint SQL + revisão de migration; ver [[002-rls-em-todas-tabelas|ADR-002]].
- **Risco:** super_admin com bypass mal escrito vê dado errado. **Mitigação:** função `is_super_admin()` SECURITY DEFINER única, testada.

## 🔁 Revisão

- **Quando revisar:** se passar de ~50 tenants ativos OU se tivermos cliente enterprise exigindo isolamento físico.
- **Como saberemos que a decisão foi errada:** vazamento real entre tenants em produção.

## 🔗 Relacionado

- [[002-rls-em-todas-tabelas|ADR-002]] — RLS é a outra metade da decisão
- [[005-infra-sempre-na-ynthegra|ADR-005]]
- [[../01-Contexto/Arquitetura]]
