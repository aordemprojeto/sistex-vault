---
title: "Runbook — Promover user para super_admin (ou outro role)"
tags:
  - runbook
  - supabase
  - auth
  - roles
  - sql
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-05-01
---

# 📖 Runbook: Promover user para `super_admin` (ou outro role)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Onboarding de novo operador YNTHEGRA (ex: dar `super_admin` para Erick)
> - Tornar tenant em `supplier_admin` da própria org
> - Promover/rebaixar role após mudança de cargo

> [!danger] Privilégio máximo
> `super_admin` bypassa RLS via [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]]. **Conceder com critério.** Auditar em [[../04-Sessoes/]].

## ✅ Pré-requisitos

- [ ] User-alvo já existe em `auth.users` (signup feito + email confirmado)
- [ ] Org-alvo já existe em `public.organizations` com `status='approved'`
- [ ] Você tem `SUPABASE_ACCESS_TOKEN` (PAT) ou acesso ao SQL Editor do Dashboard
- [ ] Roles disponíveis no enum `app_role`:
      `super_admin`, `reseller_admin`, `reseller_ops`, `reseller_finance`, `supplier_admin`, `supplier_ops`, `supplier_finance`

## 🛠️ Passos

### 1. Coletar IDs

**Pegar `user_id`:**

```sql
SELECT id, email FROM auth.users WHERE email = 'user@example.com';
```

**Pegar `org_id`:**

```sql
SELECT id, name, type, status FROM public.organizations WHERE name = 'YNTHEGRA';
```

Anotar ambos.

### 2. Inserir/atualizar role em `org_members`

**INSERT (user ainda não é membro da org):**

```sql
INSERT INTO public.org_members (user_id, org_id, role)
VALUES (
  '<user-id>',
  '<org-id>',
  'super_admin'  -- ou outro valor do enum
);
```

**UPDATE (user já é membro, trocar role):**

```sql
UPDATE public.org_members
SET role = 'super_admin'
WHERE user_id = '<user-id>'
  AND org_id = '<org-id>';
```

> [!info] Multi-org
> Um user pode ser membro de várias orgs com roles diferentes (ex: Rian é `super_admin` em YNTHEGRA + `supplier_admin` em Ferstex). Cada combinação `(user_id, org_id)` é uma linha distinta.

### 3. Validar via SELECT

```sql
SELECT om.role, o.name AS org_name, o.type
FROM public.org_members om
JOIN public.organizations o ON o.id = om.org_id
WHERE om.user_id = '<user-id>';
```

Esperado: linha com role correto.

### 4. (Opcional) Validar `is_super_admin()` retorna true

Apenas faz sentido se promoveu para `super_admin`:

```sql
SELECT public.is_super_admin('<user-id>'::uuid);
```

Esperado: `true`.

### 5. User testa login

User loga em `https://portalsistex.com`. Sidebar deve refletir o novo role:

- `super_admin`: menu PLATAFORMA, ADMINISTRAÇÃO completos
- `supplier_admin`: menu CATÁLOGO, MINHAS LOJAS, OPERAÇÕES
- `reseller_*`: menu de revendedor

## ✔️ Validação final

- [ ] `org_members` tem a linha esperada
- [ ] `is_super_admin()` retorna true (se aplicável)
- [ ] User vê o sidebar correspondente após login

## 🐞 Troubleshooting

### Sintoma: erro `invalid input value for enum app_role: "X"`
**Causa provável:** typo no nome do role.
**Correção:** consultar enum válido:
```sql
SELECT unnest(enum_range(NULL::app_role));
```

### Sintoma: user vê sidebar errado mesmo após INSERT
**Causa provável:** AuthContext do front não recarregou.
**Correção:** logout + login de novo.

### Sintoma: erro de FK ao INSERT
**Causa provável:** `user_id` ou `org_id` digitado errado.
**Correção:** re-rodar SELECT do passo 1 e copiar com cuidado.

### Sintoma: `super_admin` ainda não vê dados de outra org
**Causa provável:** policy RLS de tabela específica não inclui `OR is_super_admin()`.
**Correção:** revisar policies; ver [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]].

## 📞 Escalar para

- Se promover Stenio ou outro tenant para `super_admin`: **NUNCA** (princípio do menor privilégio + [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]). `super_admin` é exclusivo de Rian/Erick.

## 📝 Histórico de execuções

| Data | Operador | User-alvo | Role | Org | Resultado |
|---|---|---|---|---|---|
| 2026-05-01 | Rian | a.ordem.projeto@gmail.com | super_admin | YNTHEGRA | ✅ |
| 2026-05-01 | Rian | a.ordem.projeto@gmail.com | supplier_admin | Ferstex | ✅ |

## 🔗 Relacionado

- [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]]
- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]
- [[Resetar-Senha-User-via-SQL]]
- [[../01-Contexto/Pessoas]]
