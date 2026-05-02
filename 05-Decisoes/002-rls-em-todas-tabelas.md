---
title: "ADR-002 — RLS habilitada em todas as tabelas, com bypass via is_super_admin()"
tags:
  - adr
  - decisao
  - rls
  - seguranca
  - postgres
created: 2026-04-22
updated: 2026-05-01
status: accepted
numero: "002"
autor: Rian
---

# ADR-002 — RLS habilitada em todas as tabelas, com bypass via `is_super_admin()`

> [!success] Status: **accepted**

## 🌐 Contexto

[[001-multitenant-via-org-id|ADR-001]] decide que todas as tabelas têm `org_id`. Isso por si só não isola dados — qualquer query mal feita pode retornar linhas de outros tenants. Precisamos forçar isolamento no nível do banco.

Forças:
- Bug em camada de aplicação não pode comprometer isolamento entre tenants
- Super-admin YNTHEGRA legitimamente precisa cross-tenant para dashboard de plataforma
- Performance importa: policies não podem inviabilizar queries

## 🎯 Decisão

**Toda tabela com `org_id` tem RLS habilitada.** Policies usam `get_user_org_ids(auth.uid())` para filtrar por organização. O super-admin YNTHEGRA bypassa via função `is_super_admin()` (SECURITY DEFINER).

Padrão aplicado:

```sql
ALTER TABLE <tabela> ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation_select ON <tabela>
  FOR SELECT
  USING (
    org_id IN (SELECT * FROM get_user_org_ids(auth.uid()))
    OR is_super_admin()
  );

-- + INSERT/UPDATE/DELETE análogos
```

## 🔀 Alternativas consideradas

### Alternativa A — Filtro só no app (sem RLS)
- **Como funcionaria:** toda query no app inclui `where('org_id', orgId)`.
- **Prós:** zero overhead de policy.
- **Contras:** qualquer query esquecida vaza dados; bug de revisão de PR pode passar despercebido.
- **Por que não escolhemos:** isolamento de tenant é segurança crítica — não pode depender de disciplina humana.

### Alternativa B — RLS só em tabelas "sensíveis"
- **Prós:** menos overhead.
- **Contras:** classificação subjetiva, fácil de errar; toda tabela com dado de tenant é sensível.
- **Por que não escolhemos:** linha entre "sensível" e "não sensível" é falsa — qualquer coluna pode revelar negócio do tenant.

### Alternativa C — Bypass do super-admin via service_role
- **Prós:** simples (service_role já bypassa RLS).
- **Contras:** service_role não pode aparecer em Edge Function exposta a usuário; perde audit por user real.
- **Por que não escolhemos:** preferimos `is_super_admin()` que mantém `auth.uid()` real e auditável.

## 📈 Consequências

### Positivas
- Isolamento no nível mais baixo possível (banco)
- Super-admin acessa cross-tenant sem usar service_role
- Audit log mantém `auth.uid()` real do operador
- Erro de policy é detectável em smoke tests (linha não aparece quando deveria, ou aparece quando não deveria)

### Negativas / trade-offs aceitos
- Cada nova tabela exige criar policies (esquecer = bug)
- Queries que retornam vazias precisam debug com `SET role` para isolar policy vs filtro
- `is_super_admin()` SECURITY DEFINER é poder concentrado — exige testes

### Riscos
- **Risco:** policy permissiva demais (ex: `USING (true)`). **Mitigação:** lint SQL + smoke test que tenta acessar org alheia e espera `0 rows`.
- **Risco:** `is_super_admin()` retorna true para usuário comum (bug). **Mitigação:** teste unitário no helper.

## 🔁 Revisão

- **Quando revisar:** se RLS virar gargalo de performance em workload real (improvável até centenas de tenants).
- **Como saberemos que a decisão foi errada:** queries lentas atribuíveis a policies + necessidade de bypass.

## 🔗 Relacionado

- [[001-multitenant-via-org-id|ADR-001]]
- [[../01-Contexto/Arquitetura]]
- [[../07-Lessons-Learned/]]
