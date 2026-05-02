---
title: "Runbook — Aplicar migrations Supabase (db push)"
tags:
  - runbook
  - supabase
  - postgres
  - migrations
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-04-30
---

# 📖 Runbook: Aplicar migrations Supabase (`db push`)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Adicionou nova migration em `supabase/migrations/`
> - Trocou de projeto Supabase (ex: setup de novo ambiente / migração de infra)
> - Após resolver merge conflict em migrations

> [!danger] Operação que toca em schema de produção
> Migrations são aplicadas no **DB de produção**. Sempre revisar diff antes. Sem dry-run perfeito, mas `db diff` mostra o que vai mudar.

## ✅ Pré-requisitos

- [ ] Supabase CLI binário nativo
- [ ] `SUPABASE_ACCESS_TOKEN` (PAT) salvo em 1Password
- [ ] **`SUPABASE_DB_PASSWORD`** salvo em 1Password (diferente do PAT — é a senha do Postgres)
- [ ] Projeto linkado
- [ ] Repo local em `C:\Users\rianf\projetos\sistex\`
- [ ] **Backup recente do DB** (se mudança crítica)

## 🛠️ Passos

### 1. Setar credenciais

```powershell
$env:SUPABASE_ACCESS_TOKEN = "<PAT>"
$env:SUPABASE_DB_PASSWORD = "<DB-password-do-1Password>"
```

> [!important] Por que duas variáveis
> - `SUPABASE_ACCESS_TOKEN` = PAT da Management API (auth do dashboard)
> - `SUPABASE_DB_PASSWORD` = senha do Postgres (necessária para `db push` que abre conexão direta)

### 2. (Recomendado) Ver diff do que será aplicado

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex db diff --linked
```

Output: SQL que será executado. **Revisar** linha por linha:
- DROP COLUMN / DROP TABLE → atenção máxima
- ALTER TYPE em enums → pode quebrar app
- Mudança em policies RLS → revisar isolamento

### 3. Aplicar migrations

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex db push --include-all --yes
```

**Flags:**
- `--include-all` — aplica todas as migrations pendentes
- `--yes` — não pede confirmação (use com cuidado)

**Validação:** output `Finished applying migrations.`

### 4. Verificar histórico aplicado

```powershell
supabase --workdir C:\Users\rianf\projetos\sistex migration list
```

Esperado: lista de migrations com `Applied: ✓`.

### 5. Smoke test pós-migration

Dependendo do que foi aplicado:

- **Mudança em RLS:** logar como user normal e tentar acessar dados de outra org — deve retornar `0 rows`.
- **Mudança em schema de webhook:** rodar [[Smoke-Test-Webhook-Shopify]].
- **Mudança em `auth.*`:** testar login + signup.

## ✔️ Validação final

- [ ] Output `Finished applying migrations`
- [ ] `migration list` mostra todas como aplicadas
- [ ] App carrega sem erros 500 nas telas afetadas
- [ ] Smoke test relevante passou

## 🐞 Troubleshooting

### Sintoma: erro `password authentication failed`
**Causa provável:** `SUPABASE_DB_PASSWORD` errado ou ausente.
**Correção:** confirmar valor no 1Password; resetar via Dashboard → Settings → Database → Reset DB password (último recurso, invalida tudo).

### Sintoma: erro `relation already exists` ou `duplicate column`
**Causa provável:** migration tenta criar algo que já existe (estado divergente).
**Correção:** investigar se a migration é nova ou repetida. Pode ser necessário `supabase migration repair --status applied <timestamp>` para marcar como aplicada sem rodar.

### Sintoma: TTY error / "interactive prompt required"
**Causa provável:** comando esperando confirmação manual.
**Correção:** adicionar `--yes` flag.

### Sintoma: timeout / conexão recusada
**Causa provável:** firewall corporativo bloqueando porta 5432; ou Supabase em manutenção.
**Correção:** testar de outra rede; checar https://status.supabase.com.

## 📞 Escalar para

- Se migration falhar no meio (estado parcial): **NÃO** rodar de novo cegamente. Investigar com `migration list` e SQL direto via Management API antes.

## 📝 Histórico de execuções

| Data | Operador | Migration(s) | Resultado |
|---|---|---|---|
| 2026-04-30 | Rian | 45 migrations (todas, pós-migração de infra) | ✅ ok |

## 🔗 Relacionado

- [[Deploy-EdgeFunction]]
- [[Setar-Secret-EdgeFunction]]
- [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]] — verificar RLS em novas tabelas
- [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]]
