---
title: "Sessão 2026-05-01 — Migração completa e vault Obsidian"
tags:
  - sessao
  - handoff
  - migracao
  - obsidian
created: 2026-05-01
updated: 2026-05-01
operador: Rian
duracao: "~5h"
sprint: "[[../02-Sprints/MVP-Stenio-05mai]]"
---

# 📅 Sessão 2026-05-01 — Migração completa de infra + criação do vault Obsidian

> [!info] Contexto
> Sprint ativa: [[../02-Sprints/MVP-Stenio-05mai]] (T-4 dias para demo).
> Sessão anterior: 2026-04-30 (continuou madrugada adentro até esta).

## ⏱️ Duração e escopo

- **Duração total:** ~5 horas
- **Operador:** Rian
- **Escopo:** finalizar migração de infra + estabelecer memória persistente do projeto via Obsidian

## ✅ O que foi feito

### Bloco A — Migração Supabase (29-30/04 → consolidada hoje)

- [x] Criado projeto Supabase novo (`eppxxuostbtkuuftgasp`) na conta YNTHEGRA `a.ordem.projeto@gmail.com`
- [x] **45 migrations** aplicadas via `supabase db push --include-all` (DB password coletada manualmente)
- [x] **38 Edge Functions** deployadas via `supabase functions deploy`
- [x] **Secrets configurados:**
  - `ENCRYPTION_MASTER_KEY` (32 bytes random base64 — vive no 1Password)
  - `SHOPIFY_CLIENT_ID` (`5254a4d4...`)
  - `SHOPIFY_CLIENT_SECRET` (real, do app Partners)
  - `APP_BASE_URL` = `https://sistex.vercel.app`
- [x] **Smoke test webhook Shopify**: 3/3 passando ✅
- [x] `.env` local atualizado e commitado (commit `57987c6`)
- [x] Push para GitHub `main`

### Bloco B — Migração Vercel (30/04 → 01/05)

- [x] Conta Vercel YNTHEGRA criada via GitHub `aordemprojeto` (Hobby/Free) — `aordemprojeto-5068`
- [x] Repo `YNTHEGRA/sistex` importado, env vars configuradas
- [x] **Domínio antigo** (`portalsistex.com`) removido da org `synthegra` (Ferstex)
- [x] **Nameservers Hostinger** trocados de `ns1/ns2.vercel-dns.com` para `byte/pixel.dns-parking.com`
- [x] **4 DNS records** adicionados:
  - `A @ → 76.76.21.21`
  - `CNAME www → cname.vercel-dns.com`
  - `TXT _vercel → vc-domain-verify=...`
  - `TXT _vercel.www → vc-domain-verify=...`
- [x] **Loop de redirect corrigido**: apex = Production, www = Redirect 307
- [x] Após **37min** de propagação Hostinger, Vercel validou ownership
- [x] **Validação final**: `https://portalsistex.com` → 200 OK, JS aponta pra Supabase novo

### Bloco C — App Shopify Partners (30/04 → 01/05)

- [x] Conta Shopify Partners criada (org YNTHEGRA / A ORDEM, email `a.ordem.projeto@gmail.com`)
- [x] App "Sistex" criado no Dev Dashboard
- [x] Versão 1 lançada com:
  - App URL: `https://sistex.vercel.app`
  - Redirect URI: `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-connect-callback`
  - Scopes: `read_orders, read_products` ([[../05-Decisoes/006-shopify-readonly-scopes|ADR-006]])
  - API webhooks version: 2026-04
  - Distribution: Custom (multi-tenant via OAuth direto — [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]])

### Bloco D — Auth e onboarding inicial (01/05)

- [x] **Bypass do rate limit Supabase Free tier** (3-4 emails/h) via Management API:
  - `UPDATE auth.users SET email_confirmed_at = NOW() WHERE email = 'a.ordem.projeto@gmail.com'` (não `confirmed_at` — coluna gerada)
  - `UPDATE auth.users SET encrypted_password = crypt('Sistex2026!', gen_salt('bf'))`
- [x] Org `YNTHEGRA` (admin) e `Ferstex` (supplier) criadas em `public.organizations`
- [x] User `e9f5e5a2-9825-4862-afb1-df3976792003` inserido em `org_members`:
  - `super_admin` em YNTHEGRA (`24cf8d27-d291-41b8-92d8-c0b3ff6d638b`)
  - `supplier_admin` em Ferstex (`37c2fa01-adca-4320-84eb-65c46874126f`)
- [x] **`mailer_autoconfirm: true`** ativado via PATCH `/v1/projects/.../config/auth` — futuros signups (Erick, Stenio) não pegam rate limit
- [x] Rian logou em `https://portalsistex.com` → Painel Administrativo OK (sidebar DIRETOR / CATÁLOGO / MINHAS LOJAS / OPERAÇÕES / ADMINISTRAÇÃO / PLATAFORMA)

### Bloco E — Vault Obsidian para memória persistente (01/05, parte final da sessão)

- [x] Vault Obsidian estruturado em `C:\Users\rianf\Documents\YNTHEGRA\` com PARA-like prefixado
- [x] **13 pastas** criadas: `00-Inbox`, `01-Contexto`, `02-Sprints`, `03-Tenants`, `04-Sessoes`, `05-Decisoes`, `06-Runbooks`, `07-Lessons-Learned`, `08-Reunioes`, `09-Referencias`, `10-Documentos-Premium`, `99-Arquivo`, `_Templates`
- [x] **README** em cada pasta principal
- [x] **8 templates** em `_Templates/` (sprint, tenant, sessão, ADR, runbook, reunião, lesson, referência)
- [x] **MOC.md** na raiz como índice
- [x] **CLAUDE.md** populado com conteúdo de `SISTEX-CLAUDE-MASTER.md`
- [x] **4 docs derivados** em `01-Contexto/`: Negocio, Arquitetura, Infraestrutura, Pessoas
- [x] **Sprint MVP-Stenio-05mai** com plano dia-a-dia
- [x] **Tenant Ferstex** completo (README + Contato + Integracoes + Catalogo-SKUs + Reunião 2026-04-28)
- [x] **8 ADRs** numerados (001 a 008)
- [x] **8 Lessons Learned** com causa raiz
- [x] **8 Runbooks** com comandos prontos

## 🐞 Problemas encontrados e soluções aplicadas

| # | Problema | Solução | Lesson |
|---|---|---|---|
| 1 | DNS Hostinger lento (37min) | Esperar; checar authoritative direto | [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]] |
| 2 | "Domain linked to another Vercel account" | Trocar nameservers para Hostinger nativo | [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]] |
| 3 | Vercel não permite 2 contas mesma sessão | Aba anônima | [[../07-Lessons-Learned/Vercel-multi-account-mesma-sessao-impossivel]] |
| 4 | Rate limit Supabase 3-4 emails/h | SQL via Management API + `mailer_autoconfirm` | [[../07-Lessons-Learned/Supabase-rate-limit-email-3-por-hora]] |
| 5 | `confirmed_at` é coluna gerada | UPDATE em `email_confirmed_at` | [[../07-Lessons-Learned/Supabase-confirmed-at-eh-coluna-gerada]] |
| 6 | Shopify "Refresh secret" rotaciona destrutivamente | Não clicar em loop; salvar imediato no 1Password | [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]] |
| 7 | Supabase CLI via npm não funciona | Binário nativo + `SUPABASE_ACCESS_TOKEN` | [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]] |
| 8 | Lovable Cloud Supabase em org deles | Migração completa documentada | [[../07-Lessons-Learned/Lovable-cria-Supabase-em-org-deles]] |

## 📊 Estado atual ao fim da sessão

### ✅ Funcionando

- Frontend deployado em `https://portalsistex.com`
- Backend Supabase com 45 migrations + 38 Edge Functions
- Smoke test webhook Shopify: 3/3 passando
- App Shopify Partners criado, credenciais reais setadas
- Rian logado como super_admin + supplier_admin
- Auto-confirm de email ativo (próximos signups OK)
- **Independência total de Lovable e da conta Ferstex** (princípio [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]] cumprido)
- Vault Obsidian com 30+ arquivos cobrindo contexto/decisões/runbooks/lessons

### 🟡 Pendente (bloqueia demo do Stenio)

- [ ] **Conectar loja Shopify do Stenio** — falta `xxx.myshopify.com` (Stenio fornece)
- [ ] **OAuth Bling** pelo painel Ferstex — Stenio reconecta
- [ ] **OAuth Melhor Envio** pelo painel Ferstex — Stenio reconecta
- [ ] **Cadastrar 5-10 produtos Ferstex com SKU** que casam com loja Shopify
- [ ] **Smoke test E2E** manual antes da demo

### 🟠 Pendente (não-crítico mas relevante)

- [ ] `www.portalsistex.com` ainda mostra "Verification Needed" (apex está OK)
- [ ] Frontend não tem tela "Alterar senha"
- [ ] Onboarding wizard de revendedor aparece pra super_admin (UX bug)

## 🤝 Handoff para próximo operador

> [!important] Próximo operador: **Erick** (ou Rian na próxima sessão)

### Estado em uma frase
Infra 100% migrada para conta YNTHEGRA, app Shopify funcional com smoke test passando, Rian logado. Falta o lado Stenio: domínio da loja, Bling, ME, produtos com SKU.

### Próximas ações em ordem de prioridade

1. **Contatar Stenio via WhatsApp** pedindo:
   - Domínio Shopify interno da loja (`xxx.myshopify.com`)
   - Disponibilidade pra reconectar Bling e Melhor Envio
2. **Quando Stenio responder**, executar [[../06-Runbooks/Conectar-Shopify-Tenant]] para conectar a loja
3. **Cadastrar produtos** (SQL ou via UI) com SKU casando — ver [[../03-Tenants/Ferstex/Catalogo-SKUs]]
4. **Smoke test E2E** depois que tudo estiver conectado
5. **Demo dia 2026-05-05** — Stenio compra na própria loja, acompanhamos

### Bloqueios e atenção

> [!danger] Credenciais expostas em chat — rotacionar quando estabilizar
> - Senha `Sistex2026!` do user `a.ordem.projeto@gmail.com` — vazada
> - Senha do user `rianfort916@gmail.com` — vazada
> - Service Role Key e PAT do Supabase — vazados em chat anterior

> [!warning] Apagar quando confirmar 1Password
> - `C:\Users\rianf\Documents\YNTHEGRA\sistex-secrets-PRODUCAO.txt`
> - Projeto Vercel antigo na org `synthegra`
> - Projeto Supabase antigo `ngeigfbffuqsviruhrmc` (Lovable Cloud)

### Comandos prontos para retomar

```powershell
# Setup do ambiente
$env:SUPABASE_ACCESS_TOKEN = "<PAT-1password>"
$env:SUPABASE_DB_PASSWORD = "<DB-password-1password>"

# Validar status do site
curl -sI "https://portalsistex.com" --max-time 10

# Smoke test webhook Shopify
$env:SHOPIFY_CLIENT_SECRET = "<secret-real>"
node C:\Users\rianf\projetos\sistex\scripts\smoke-test-shopify-webhook.mjs

# Listar secrets configurados
supabase --workdir C:\Users\rianf\projetos\sistex secrets list

# Ver organizações
$body = @{ query = "SELECT id, name, type, status FROM public.organizations;" } | ConvertTo-Json
Invoke-RestMethod -Method POST `
  -Uri "https://api.supabase.com/v1/projects/eppxxuostbtkuuftgasp/database/query" `
  -Headers @{ "Authorization" = "Bearer $env:SUPABASE_ACCESS_TOKEN"; "Content-Type" = "application/json" } `
  -Body $body
```

## 📝 Notas livres

- Vault Obsidian agora é a **memória de longo prazo** do projeto. Toda sessão futura deve ler [[../01-Contexto/CLAUDE]] primeiro e atualizar arquivos relevantes ao final.
- Master file `SISTEX-CLAUDE-MASTER.md` continua na raiz como backup; apagar depois de 1ª revisão completa do vault.
- **Stenio não foi avisado** ainda da migração — não tem efeito visível pra ele (URL é a mesma), mas vale comunicar quando contatar pra pedir o `xxx.myshopify.com`.

## 🔗 Relacionado

- Sprint: [[../02-Sprints/MVP-Stenio-05mai]]
- Tenant: [[../03-Tenants/Ferstex/README]]
- Reunião que originou: [[../03-Tenants/Ferstex/Reunioes/2026-04-28-alinhamento-prazo]]
- ADRs criadas: [[../05-Decisoes/]]
- Lessons criadas: [[../07-Lessons-Learned/]]
- Runbooks criados: [[../06-Runbooks/]]
- Contexto mestre: [[../01-Contexto/CLAUDE]]
