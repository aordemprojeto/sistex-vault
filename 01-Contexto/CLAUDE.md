---
title: CLAUDE.md — Contexto Mestre SISTEX
tags:
  - contexto
  - master
created: 2026-05-01
updated: 2026-05-01
status: active
---

# SISTEX — Contexto Mestre para Claude

> **Documento mestre** do projeto SISTEX/YNTHEGRA. Concentra TODO contexto necessário para qualquer instância de Claude operar o projeto sem perda de continuidade entre sessões.
>
> **Última grande atualização:** 01/05/2026 (após migração Supabase + Vercel + DNS para conta YNTHEGRA própria)
> **Status do MVP:** Backend migrado, frontend deploado, app Shopify criado, falta conectar loja Stenio + Bling + Melhor Envio
> **Demo com Stenio agendada:** terça-feira 05/05/2026

---

## 📖 Índice

1. [O Negócio](#1-o-negócio) — também em [[Negocio]]
2. [Os Sócios](#2-os-sócios) — também em [[Pessoas]]
3. [Arquitetura Técnica](#3-arquitetura-técnica) — também em [[Arquitetura]]
4. [Infraestrutura — URLs, IDs e Credenciais](#4-infraestrutura--urls-ids-e-credenciais) — também em [[Infraestrutura]]
5. [Histórico Resumido (até 01/05/2026)](#5-histórico-resumido)
6. [Estado Atual do Sistema](#6-estado-atual-do-sistema)
7. [Plano de 5 Dias até Demo Stenio](#7-plano-de-5-dias-até-demo-stenio) — também em [[../02-Sprints/MVP-Stenio-05mai|Sprint MVP-Stenio-05mai]]
8. [Stack Tecnológica](#8-stack-tecnológica)
9. [Convenções do Projeto](#9-convenções-do-projeto)
10. [Features Implementadas](#10-features-implementadas)
11. [Roadmap Pendente](#11-roadmap-pendente)
12. [Padrões de Segurança](#12-padrões-de-segurança) — ADRs em [[../05-Decisoes/]]
13. [Lessons Learned](#13-lessons-learned) — detalhadas em [[../07-Lessons-Learned/]]
14. [TODOs e Pendências Conhecidas](#14-todos-e-pendências-conhecidas)
15. [Comandos e Scripts Úteis](#15-comandos-e-scripts-úteis) — runbooks em [[../06-Runbooks/]]
16. [Protocolo de Handoff Rian ↔ Erick](#16-protocolo-de-handoff-rian--erick)
17. [Regras Críticas (NUNCA / SEMPRE)](#17-regras-críticas)

---

## 1. O Negócio

### O que é YNTHEGRA?

**YNTHEGRA** é a empresa-plataforma. É a "casa" onde mora a infraestrutura de software (Supabase, Vercel, Stripe, GitHub, domínios). YNTHEGRA é dona do produto SISTEX.

### O que é SISTEX?

**SISTEX** é uma plataforma SaaS multi-tenant para **dropshipping de moda fitness**. Conecta fornecedores (que produzem) a revendedores (que vendem online) de forma automatizada.

### Modelo de tenants

- **Tenant tipo "supplier"** (fornecedor) — Ferstex é o primeiro
- **Tenant tipo "reseller"** (revendedor / drop) — paga R$130/mês pra usar
- **Tenant tipo "admin"** (YNTHEGRA) — opera a plataforma

### Quem é a Ferstex?

Fabricante de moda fitness em Brusque/SC. **Dono: Stenio.** É o **primeiro tenant supplier** e parceiro-piloto da YNTHEGRA. O catálogo Ferstex é o primeiro a estar disponível pra revenda na plataforma.

### Modelo comercial atual (acordado com Stenio)

- **Licença vitalícia para Ferstex:** R$60.000 (pagamento único)
- **Manutenção mensal Ferstex:** R$2.500/mês
- **Drops/revendedores:** R$130/mês cada (trial 10 dias) — receita recorrente da YNTHEGRA
- **Split por pedido:** 70% YNTHEGRA / 30% fornecedor (editável por tenant)
- **Plano comercial completo:** ver `precificacao-sistex-v2.html` em [[../10-Documentos-Premium/]]

### Dor #1 que o SISTEX resolve

**Relatório consolidado de produção** (Motor de Produção). Antes do SISTEX, fornecedores tipo Ferstex precisavam compilar manualmente os pedidos de várias plataformas (Shopify, ML, Shopee) num único relatório de produção da fábrica. SISTEX automatiza isso. **Esse é o motor de retenção** — sem ele, drops cancelam e fornecedores abandonam.

### Estratégia de retenção

SISTEX precisa virar **infraestrutura do negócio**, não ferramenta opcional. Quanto mais integrações (Bling, Melhor Envio, marketplaces) o tenant ativa pelo SISTEX, mais doloroso fica sair. Cada integração ativada é um "lock-in" positivo.

---

## 2. Os Sócios

### Rian (criador do projeto)

- **Email principal:** `a.ordem.projeto@gmail.com`
- **GitHub:** `aordemprojeto`
- **Outras contas que aparecem:** `rianfort916@gmail.com` (conta de teste, senha foi compartilhada uma vez no chat — TROCAR)
- **Perfil técnico:** **NÃO programador**. Aprendeu MVP via Lovable. Hoje opera Claude Code via terminal e Claude in Chrome.
- **Estilo de comunicação:** prefere clareza, plano de ação concreto, sem teoria longa.

### Erick (sócio operacional)

- **Email:** `erickluisblitz@gmail.com` (também aparece `erickluis@gmail.com`)
- **Perfil técnico:** Mesmo nível do Rian. Não programa.
- **Atua quando:** Rian está ocupado e vice-versa. Alternam.

### Como operam

- **Rian e Erick alternam** quem cuida do SISTEX em cada sessão de trabalho.
- Ambos têm acesso ao Claude Code CLI no Windows do Rian (`C:\Users\rianf\projetos\sistex\`).
- **Quando um termina sessão**, deve gerar **resumo de handoff** para o outro retomar (ver seção 16 e [[../04-Sessoes/]]).

### Email da empresa (não pessoal)

- `ynthegra@gmail.com` — usado como contato de API no Shopify Partners

---

## 3. Arquitetura Técnica

### Diagrama mental

```
┌─────────────────────────────────────────────────────┐
│  USUÁRIOS                                           │
│  • Cliente final (compra na loja Shopify do Stenio) │
│  • Drop/revendedor (paga R$130/mês)                 │
│  • Admin Ferstex (Stenio — gerencia produtos)       │
│  • Super-admin YNTHEGRA (Rian/Erick)                │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  FRONTEND — React 18 + Vite + TS                    │
│  Hospedado em: Vercel (conta aordemprojeto-5068)    │
│  URL: https://portalsistex.com                      │
│  URL alt: https://sistex.vercel.app                 │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  BACKEND — Supabase                                 │
│  Project ref: eppxxuostbtkuuftgasp                  │
│  Conta: a.ordem.projeto@gmail.com                   │
│  Região: South America (São Paulo)                  │
│  • Postgres (45 migrations)                         │
│  • Edge Functions Deno (38 functions)               │
│  • Auth (email/password)                            │
│  • Storage (imagens de produto)                     │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│  INTEGRAÇÕES EXTERNAS                               │
│  • Shopify (read-only) — pedidos vindos da loja    │
│  • Bling (OAuth) — NF-e e estoque                   │
│  • Melhor Envio (OAuth) — etiquetas                 │
│  • Mercado Pago (Marketplace) — pagamento drops    │
│  • Mercado Livre (futuro)                           │
│  • Shopee (futuro)                                  │
│  • Stripe (futuro — assinatura drops em KYC)        │
└─────────────────────────────────────────────────────┘
```

### Princípio multi-tenant

- **TODA tabela operacional tem `org_id`** (foreign key pra `organizations.id`)
- **RLS habilitada em todas as tabelas** com policies que filtram por `get_user_org_ids(auth.uid())`
- **Super-admin YNTHEGRA** bypassa RLS via função `is_super_admin()`

Ver [[../05-Decisoes/001-multitenant-via-org-id|ADR-001]] e [[../05-Decisoes/002-rls-em-todas-tabelas|ADR-002]].

### Princípio "infra na YNTHEGRA"

> [!danger] REGRA INVIOLÁVEL
> Toda conta de infraestrutura crítica é da YNTHEGRA, não de fornecedores ou drops.

- ✅ Supabase: conta `a.ordem.projeto@gmail.com`
- ✅ Vercel: conta `aordemprojeto-5068` (criada em 01/05/2026)
- ✅ GitHub: org `YNTHEGRA`
- ✅ Domínio: `portalsistex.com` registrado em Hostinger sob YNTHEGRA
- ✅ Shopify Partners: org YNTHEGRA (criada em 30/04/2026)

**Se algum dia você ver infra em conta de fornecedor (ex: Ferstex), É BUG ESTRATÉGICO** — fornecedor pode bloquear acesso da YNTHEGRA à sua própria infra. Migrar imediatamente.

Ver [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]].

---

## 4. Infraestrutura — URLs, IDs e Credenciais

### Frontend (Vercel)

| Atributo | Valor |
|---|---|
| Provider | Vercel |
| Org/team | `aordemprojeto-5068` (Hobby/Free, conta YNTHEGRA) |
| Projeto | `sistex` |
| URL primária | `https://portalsistex.com` |
| URL secundária | `https://sistex.vercel.app` (sempre atualizada) |
| URL `www` | `https://www.portalsistex.com` (redirect 307 → apex) |
| Repo conectado | `YNTHEGRA/sistex` (branch `main`) |
| Auto-deploy | Sim, push em `main` dispara build |

### Backend (Supabase)

| Atributo | Valor |
|---|---|
| Provider | Supabase |
| Org | YNTHEGRA.Org (Free) |
| Project ref | `eppxxuostbtkuuftgasp` |
| Project URL | `https://eppxxuostbtkuuftgasp.supabase.co` |
| Anon key (público) | `sb_publishable_TxSjFY2FLS2YdO7gBo4LzA_gNrKnbeP` |
| Service role | `<no-1password>` (formato: `sb_secret_...`) |
| Database password | `<no-1password>` |
| Region | South America (São Paulo) - `sa-east-1` |
| Dashboard | https://supabase.com/dashboard/project/eppxxuostbtkuuftgasp |

### Domínio (Hostinger)

| Atributo | Valor |
|---|---|
| Domínio | `portalsistex.com` |
| Registrar | Hostinger |
| Nameservers | `byte.dns-parking.com` + `pixel.dns-parking.com` (Hostinger nativos) |
| DNS records principais | `A @ → 76.76.21.21` · `CNAME www → cname.vercel-dns.com` · 2 TXT pra verificação Vercel |

Ver [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]].

### Repositório (GitHub)

| Atributo | Valor |
|---|---|
| Org | YNTHEGRA |
| Repo | YNTHEGRA/sistex |
| URL | https://github.com/YNTHEGRA/sistex |
| Visibilidade | Privado |
| Branch principal | `main` |

### App Shopify Partners

| Atributo | Valor |
|---|---|
| Org Partners | YNTHEGRA / A ORDEM |
| Email Partners | `a.ordem.projeto@gmail.com` |
| Nome do app | Sistex |
| Client ID | `5254a4d49feb9801b1cbe8263e98b76c` |
| Client Secret | `<no-1password>` (formato `shpss_...`) |
| App URL | `https://sistex.vercel.app` |
| Redirect URI | `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-connect-callback` |
| Scopes | `read_orders, read_products` |
| API webhooks version | 2026-04 |
| Email contato API | `ynthegra@gmail.com` |
| Distribution | A configurar (custom distribution OU OAuth direto) |

Ver [[../05-Decisoes/006-shopify-readonly-scopes|ADR-006]] e [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]].

### Personal Access Tokens (PATs)

| Provider | Status |
|---|---|
| Supabase PAT (`sbp_...`) | Existe, salvo em `<1password>`. Foi compartilhado em chat — **rotacionar quando estabilizarmos** |
| GitHub PAT | Não criamos (gh CLI não está autenticada no Windows do Rian) |
| Vercel CLI | Instalado (`v52.0.0`) mas sem login interativo |

### Secrets configurados no Supabase Edge Functions

| Secret | Status |
|---|---|
| `ENCRYPTION_MASTER_KEY` | ✅ Setado (32 bytes random base64) |
| `SHOPIFY_CLIENT_ID` | ✅ Setado (`5254a4d4...`) |
| `SHOPIFY_CLIENT_SECRET` | ✅ Setado (real, do app Partners) |
| `APP_BASE_URL` | ✅ `https://sistex.vercel.app` (atualizar pra `https://portalsistex.com` quando confortável) |
| `MP_WEBHOOK_SECRET` | ⏳ Não configurado (quando ativar Mercado Pago) |
| `ML_WEBHOOK_SECRET` | ⏳ Não configurado |
| `SHOPEE_WEBHOOK_SECRET` | ⏳ Não configurado |
| `ME_WEBHOOK_SECRET` | ⏳ Não configurado (quando ativar Melhor Envio) |
| `BLING_CLIENT_ID/SECRET` | ⏳ Não configurado (quando ativar Bling) |
| `SUPABASE_URL`, `SUPABASE_*_KEY` | ✅ Auto-managed pela Supabase |

### Vercel — Environment Variables

| Var | Valor |
|---|---|
| `VITE_SUPABASE_URL` | `https://eppxxuostbtkuuftgasp.supabase.co` |
| `VITE_SUPABASE_PUBLISHABLE_KEY` | `sb_publishable_TxSjFY2FLS2YdO7gBo4LzA_gNrKnbeP` |
| `ID_DO_PROJETO_SUPABASE_VITE` | `eppxxuostbtkuuftgasp` (nome em PT, herdado do Lovable) |

### Organizações (rows na tabela `public.organizations`)

| ID | Nome | Tipo | Status |
|---|---|---|---|
| `24cf8d27-d291-41b8-92d8-c0b3ff6d638b` | YNTHEGRA | admin | approved |
| `37c2fa01-adca-4320-84eb-65c46874126f` | Ferstex | supplier | approved |

### Users iniciais (em `auth.users`)

| Email | User ID | Roles |
|---|---|---|
| `a.ordem.projeto@gmail.com` | `e9f5e5a2-9825-4862-afb1-df3976792003` | super_admin (YNTHEGRA) + supplier_admin (Ferstex) |

---

## 5. Histórico Resumido

### Bloco 1 — MVP Inicial via Lovable (fevereiro–abril 2026)

- Projeto criado no Lovable com 10 blocos de feature
- Lovable Cloud provisionou Supabase (`ngeigfbffuqsviruhrmc`) + Vercel (org `synthegra`, conta `grupoferstex@gmail.com`)
- Domínio `portalsistex.com` configurado
- Bloco 11 (Motor de Produção) finalizado — relatório consolidado com export PDF/XLS
- Bloco 12 (Super Admin YNTHEGRA) finalizado

### Bloco 2 — Migração para GitHub + Vercel + Custom Domain (abril 2026)

- Saída do Lovable do front-end → repo `YNTHEGRA/sistex` no GitHub
- Vercel deploy automático conectado ao repo
- DNS apontando para Vercel via nameservers `ns1.vercel-dns.com` / `ns2.vercel-dns.com`

### Bloco 3 — Setup Claude Code CLI (abril 2026)

- Claude Code instalado no Windows do Rian (`C:\Users\rianf\projetos\sistex\`)
- `CLAUDE.md` criado no repo com contexto persistente
- Sessões via terminal funcionando

### Bloco 4 — Auditoria e Hardening de Segurança (22-23/04/2026)

**Fase A (críticos):**
- Validação HMAC em TODOS os webhooks (MP, ML, Shopee, Melhor Envio, Bling, Shopify) — formatos específicos por plataforma em `_shared/hmac.ts`
- Schemas Zod centralizados em `_shared/validation.ts` (`zUUID`, `zMoneyCents`, `zCEP`, etc.)
- SQL injection fix em `api-gateway` (UUID validado, `parsePositiveInt`)
- SQL injection fix em `search` (`escapeIlike()` escapa `%` e `_`)

**Fase B (altos):**
- RLS em todas as tabelas com `get_user_org_ids(auth.uid())`
- `is_super_admin()` SECURITY DEFINER para bypass legítimo
- Tokens OAuth criptografados em AES-GCM (Bling, Shopify, MP, ML, Shopee, Melhor Envio)
- Audit log com `correlation_id` + `trace_id` em `audit_log`
- Idempotência em pagamentos (`UNIQUE` em `purchase_orders.idempotency_key` + `payment_batches.idempotency_key`)

**Fase C (médios):**
- Replay protection em webhooks (janela 10min, header `X-Shopify-Webhook-Triggered-At`, etc.)
- Wallet locks com `pg_advisory_xact_lock` durante batch approvals
- HKDF-SHA256 para derivação de chave (`encryption.ts` v2 com prefixo `v2:`, fallback v1 para legado)

### Bloco 5 — Pricing Strategy (28/04/2026)

- Documentos premium criados em [[../10-Documentos-Premium/]]:
  - `precificacao-sistex.html` (v1)
  - `precificacao-sistex-v2.html` (final, McKinsey-style)
  - `status-sistex-ferstex.html` (apresentação para reunião com Stenio)
- Decisão: Ferstex paga R$60k vitalício + R$2.5k/mês manutenção. Drops pagam R$130/mês.

### Bloco 6 — Reunião Stenio (28/04/2026)

- Stenio estava desanimado com a demora (2 meses).
- Reunião alinhou expectativas. Stenio pediu **MVP demonstrável** (não lançamento oficial), apenas o fluxo principal rodando com a loja Shopify dele.
- **Demo agendada: terça 05/05/2026.**

Ver [[../03-Tenants/Ferstex/Reunioes/2026-04-28-alinhamento-prazo|ata da reunião]].

### Bloco 7 — Migração Total para conta YNTHEGRA própria (29-30/04/2026 — sessão crítica)

**Problema descoberto:**
- Supabase `ngeigfbffuqsviruhrmc` estava em **org Lovable** (nem Rian conseguia acessar pelo dashboard)
- Vercel `synthegra` estava em conta `grupoferstex@gmail.com` (email da Ferstex, não YNTHEGRA)
- DNS Vercel-managed bloqueava `Vercel: This domain is linked to another Vercel account`

**Migração feita:**
1. Criado projeto Supabase novo (`eppxxuostbtkuuftgasp`) na conta `a.ordem.projeto@gmail.com`
2. 45 migrations aplicadas via `supabase db push` (DB password coletada manualmente)
3. 38 Edge Functions deployadas via `supabase functions deploy`
4. Secrets setados (`ENCRYPTION_MASTER_KEY` random + `SHOPIFY_CLIENT_SECRET` temp)
5. Smoke test webhook Shopify: 3/3 passando (HMAC válido → 200, HMAC ruim → 401, replay → 401)
6. `.env` local atualizado e commitado (commit `57987c6`)
7. Push para GitHub `main`

**Vercel migrado:**
8. Conta nova Vercel criada com GitHub `aordemprojeto` (Hobby/Free) — `aordemprojeto-5068`
9. Repo importado, env vars configuradas, primeiro deploy bem-sucedido
10. Domínio antigo removido da org `synthegra`
11. Domínio novo adicionado na conta YNTHEGRA — verification needed
12. Nameservers Hostinger trocados de `ns1/ns2.vercel-dns.com` → `byte/pixel.dns-parking.com`
13. 4 DNS records adicionados no Hostinger:
    - `A @ → 76.76.21.21`
    - `CNAME www → cname.vercel-dns.com`
    - `TXT _vercel → vc-domain-verify=portalsistex.com,...`
    - `TXT _vercel.www → vc-domain-verify=www.portalsistex.com,...`
14. Loop de redirect corrigido: `portalsistex.com = Production`, `www = Redirect 307 → apex`
15. Após 37 minutos de propagação interna do Hostinger, Vercel validou ownership

**Validação final:**
- `https://portalsistex.com` → 200 OK
- JavaScript bundled aponta para `eppxxuostbtkuuftgasp.supabase.co` (Supabase novo)
- Smoke test passa com secret real

### Bloco 8 — App Shopify Partners (30/04 e 01/05/2026)

- Conta Shopify Partners criada (org YNTHEGRA, email `a.ordem.projeto@gmail.com`)
- App "Sistex" criado no Dev Dashboard
- Versão 1 lançada (`v1-mvp-ferstex` ou similar) com:
  - App URL: `https://sistex.vercel.app`
  - Redirect URI: `https://eppxxuostbtkuuftgasp.supabase.co/functions/v1/shopify-connect-callback`
  - Scopes: `read_orders, read_products`
  - API webhooks: 2026-04
- Client ID e Client Secret coletados e setados nos secrets do Supabase

### Bloco 9 — Auth e Onboarding inicial (01/05/2026)

**Problema:** rate limit de email do Supabase (3-4/h) bloqueou confirmação de cadastro do Rian.

**Solução aplicada via Management API:**
1. Email confirmado manualmente via SQL (`UPDATE auth.users SET email_confirmed_at = NOW()`)
2. Senha setada via SQL com `crypt('Sistex2026!', gen_salt('bf'))` — **TROCAR — foi compartilhada no chat**
3. Org `YNTHEGRA` (admin) e `Ferstex` (supplier) criadas
4. User `e9f5e5a2-...` inserido em `org_members` como super_admin (YNTHEGRA) + supplier_admin (Ferstex)
5. `mailer_autoconfirm` ativado via PATCH `/v1/projects/.../config/auth` — futuros signups (Erick, Stenio) não pegam rate limit

**Validação:** Rian logou em `https://portalsistex.com`, viu Painel Administrativo com sidebar completo (DIRETOR, CATÁLOGO, MINHAS LOJAS, OPERAÇÕES, ADMINISTRAÇÃO, PLATAFORMA).

### Bloco 10 — Acoplagem ao Obsidian para memória persistente (01/05/2026)

- Sessão de chat 1M chegando ao limite
- Decidido criar este documento mestre para Obsidian — contexto infinito entre sessões

---

## 6. Estado Atual do Sistema

### O que está funcionando ✅

- Frontend deployado em `https://portalsistex.com`
- Backend Supabase com 45 migrations + 38 Edge Functions
- Smoke test webhook Shopify: 3/3 passando
- App Shopify Partners criado com credenciais reais setadas
- Rian logado como super_admin + supplier_admin
- Auto-confirm de email ativo (próximos signups sem rate limit)
- Independência total de Lovable e da conta Ferstex (Vercel synthegra)

### O que NÃO está funcionando / pendente 🟡

- **Loja Shopify do Stenio não conectada** — falta domínio `xxxx.myshopify.com` da loja dele
- **Bling não conectado** — vai conectar quando logar como supplier_admin Ferstex
- **Melhor Envio não conectado** — idem
- **Refresh do `www.portalsistex.com`** ainda mostra "Verification Needed" (o `apex` está OK e funcional)
- **Frontend não tem tela "Alterar senha"** (Rian não conseguiu trocar a `Sistex2026!` compartilhada)
- **Onboarding wizard de revendedor** aparece pra super_admin (UX bug — não bloqueia, mas confunde)

### O que está parcialmente funcionando 🟠

- **Vercel CLI** instalado mas sem login interativo (mesma limitação que Supabase teve antes do PAT)
- **Supabase CLI** funcional via `SUPABASE_ACCESS_TOKEN` env var

---

## 7. Plano de 5 Dias até Demo Stenio

> **Demo:** terça-feira 05/05/2026
> **Hoje (referencial):** sexta-feira 01/05/2026
> **Dias úteis restantes:** 4

| Dia | Status | Tarefa |
|---|---|---|
| **Dia 1** | ✅ Concluído | Migração Supabase + Vercel + DNS + Smoke test |
| **Dia 2** | 🟡 Em andamento | App Shopify Partners criado + secrets setados. Falta conectar loja do Stenio |
| **Dia 3** | ⏳ Pendente | OAuth Melhor Envio + Bling pelo painel admin Ferstex (Stenio reconecta) |
| **Dia 4** | ⏳ Pendente | Smoke test E2E manual: compra na loja Shopify → vê pedido no SISTEX → gera etiqueta → relatório de produção |
| **Dia 5** | ⏳ Pendente | Demo ao vivo com Stenio: ele compra na própria loja, acompanhamos juntos |

### Foco do MVP (combinado com Stenio)

**NÃO precisa lançamento oficial.** Stenio só quer ver o **fluxo principal funcionando** com a loja dele. Tudo que estiver fora desse fluxo único pode ficar pra depois:

```
Cliente compra na loja Shopify do Stenio
        ↓
Webhook chega no SISTEX (HMAC validado)
        ↓
SKU matching → produto Ferstex identificado
        ↓
Pedido aparece no painel admin Stenio
        ↓
Stenio gera etiqueta Melhor Envio (1 clique)
        ↓
Pedido entra no Motor de Produção (relatório consolidado)
```

Se esse fluxo único rodar **uma vez na demo**, é sucesso.

Plano detalhado: [[../02-Sprints/MVP-Stenio-05mai|Sprint MVP-Stenio-05mai]].

---

## 8. Stack Tecnológica

### Frontend

| Camada | Tecnologia |
|---|---|
| Framework | React 18 |
| Linguagem | TypeScript |
| Bundler | Vite (SWC) |
| UI Components | shadcn/ui (Radix) + Tailwind CSS |
| CSS adicional | styled-components |
| Roteamento | React Router DOM v6 |
| State / Server | TanStack React Query |
| Formulários | React Hook Form + Zod |
| Gráficos | Recharts |
| PDF | jsPDF + jspdf-autotable |
| Planilhas | xlsx |
| Animação | Framer Motion |
| Testes | Vitest + Testing Library |
| Package manager | Bun (com fallback npm) |
| i18n | Custom PT-BR |

### Backend

| Camada | Tecnologia |
|---|---|
| Database | Postgres (via Supabase) |
| Edge Functions | Deno (Supabase Functions) |
| Auth | Supabase Auth (email/password) |
| Storage | Supabase Storage (imagens) |
| Realtime | Supabase Realtime (não usado ainda) |

### Roles do enum `app_role`

```
super_admin       — YNTHEGRA platform
reseller_admin    — admin de revendedor
reseller_ops      — operação de revendedor (default novos users)
reseller_finance  — financeiro de revendedor
supplier_admin    — admin de fornecedor (ex: Stenio)
supplier_ops      — operação de fornecedor
supplier_finance  — financeiro de fornecedor
```

### Tipos de organização

```
admin     — YNTHEGRA
reseller  — drops/revendedores
supplier  — fornecedores (Ferstex)
```

### Status de organização

```
pending → review → approved → suspended
```

### Estrutura principal do repositório

```
sistex/
├── src/
│   ├── pages/          # 48 telas (rotas em PT-BR)
│   ├── components/     # Componentes por domínio (ui, orders, catalog, etc.)
│   ├── hooks/          # 36+ custom hooks
│   ├── contexts/       # AuthContext
│   ├── integrations/   # Cliente e tipos Supabase
│   ├── lib/            # Utilitários (PDF, parser, pipeline)
│   ├── i18n/           # Traduções PT-BR
│   └── theme/          # Tema sidebar
├── supabase/
│   ├── config.toml     # Configuração das functions (verify_jwt por function)
│   ├── migrations/     # 45 SQL migrations
│   └── functions/      # 38 Edge Functions
│       └── _shared/    # hmac, validation, encryption, audit, replay_protection, shopify, etc.
├── scripts/
│   └── smoke-test-shopify-webhook.mjs   # 3 testes do webhook
├── CLAUDE.md           # Contexto pra Claude Code (versão dentro do repo)
└── .env                # VITE_SUPABASE_* (commitado, são chaves públicas)
```

---

## 9. Convenções do Projeto

### Rotas em português

```
/painel/admin
/pedidos
/catálogo-ferstex
/carrinho
/integrações/shopify
/saude   (não "salsicha" — bug de renderização do Lovable mostrava errado)
```

### Mensagens de UI em PT-BR

Toda interface visível pro usuário é em português brasileiro.

### Commit messages em português com prefixo semântico

```
feat: nova feature
fix: correção de bug
security: hardening de segurança
observability: logs/audit/tracing
refactor: reescrita sem mudar comportamento
chore: tarefa operacional (deps, config, etc)
infra: mudanças de infraestrutura (deploy, secrets, env)
docs: documentação
```

Exemplo real: `infra: migração Supabase para conta YNTHEGRA própria` (commit `57987c6`)

### Co-author em commits feitos via Claude Code

```
Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
```

### Mobile-first

Mínimo 375px de largura. Testes feitos em mobile e desktop.

### Multi-tenant via `organization_id`

Todas as tabelas relevantes têm `org_id` como FK.

### Cores

- **Áreas Ferstex:** amarelo `#F5B800` + preto
- **Áreas YNTHEGRA / super-admin:** paleta neutra azul/cinza navy `#0f2744` + dourado `#a47e3c`

### Padrões de design para documentos premium (HTMLs em [[../10-Documentos-Premium/]])

- Tema McKinsey/Economist editorial
- Tipografia Inter ou system fonts
- Print-optimized (A4, page-break management)
- Zero JS (fonts só via CSS)
- A11y AA compliant

---

## 10. Features Implementadas

### 12 blocos completos

1. **SKU Matching** — cascade de 6 níveis pra mapear SKU da loja com produto Ferstex
2. **Webhook Shopify** — resposta 200 imediata + fila async de processamento
3. **Configurações** — MP, ViaCEP, Bling, frete
4. **Assinatura Stripe** — trial 10 dias, R$130/mês (em KYC)
5. **Controle de acesso por role** — super_admin / supplier_* / reseller_* / revendedor
6. **Catálogo e pedido direto** — drop compra do catálogo Ferstex
7. **Onboarding revendedores** — wizard 4 passos (mostra mesmo pra super_admin — bug)
8. **Estados vazios e feedback** — UX polida pra "sem dados ainda"
9. **Dashboard admin Ferstex** — métricas + Recharts
10. **Checklist final de lançamento**
11. **Motor de Produção** — relatório consolidado com export PDF/XLS (a feature mais importante pra Stenio)
12. **Super Admin YNTHEGRA** — gerenciar tenants, ver dashboard cross-tenant, financeiro plataforma

### Edge Functions principais

```
shopify-connect-start      shopify-connect-callback   shopify-disconnect
shopify-webhooks           shopify-sync               
bling-oauth                bling-orders               bling-nfe              bling-webhook
melhorenvio-oauth          melhorenvio-quote          melhorenvio-label
mp-webhook                 stripe-checkout            stripe-webhook
marketplace-connect-start  marketplace-connect-callback marketplace-disconnect
oauth-start                oauth-callback             token-refresh
api-gateway                api-keys                   admin-settings
search                     calculate-shipping         frenet-quote
create-direct-order        create-purchase            create-payment-batch
job-worker                 reprocess-job              webhook-receiver
save-channel-credentials   daily-production-snapshot  seed-demo-data
frenet-test
```

---

## 11. Roadmap Pendente

### Próximos blocos de feature

- **Bloco 13** — White-label multi-tenant (subdomínios próprios por fornecedor, branding personalizado)
- **Bloco 14** — Sistema de convite de revendedor (email + link de cadastro)
- **ALAVANCA 2** — Publicação 1-clique em marketplaces (Shopify App OAuth)

### Outras pendências críticas

- ⚠️ **Tela "Alterar senha" no frontend** — bloqueia Stenio/Erick de mudarem senhas autonomamente
- ⚠️ **Templates de email transacional PT-BR** — confirmação de pedido, etiqueta gerada, etc.
- ⚠️ **Contrato YNTHEGRA × Ferstex** — com advogado SaaS
- 🟡 Configurar Stripe quando KYC liberar
- 🟡 Termos de Uso + Política de Privacidade + LGPD consent
- 🟡 Monitoramento (Sentry) + Analytics (PostHog ou Plausible)
- 🟡 Migrar projeto Vercel pra org YNTHEGRA dedicada (atualmente em conta pessoal `aordemprojeto-5068`)

---

## 12. Padrões de Segurança

### HMAC validation em webhooks

Helpers em `supabase/functions/_shared/hmac.ts` (com testes em `hmac.test.ts`):

- `hmacSha256Hex(secret, payload)` — pra Bling, ML, Melhor Envio
- `hmacSha256Base64(secret, payload)` — pra Shopee, Shopify
- `validateMercadoPagoSignature` — formato MP (`ts=X,v1=Y`)
- `validateHmacHex` — genérico hex (suporta prefixo `sha256=`)
- `validateHmacBase64` — genérico base64
- `timingSafeEqual` — comparação constante (evita timing attack)

### Replay protection

Helper em `supabase/functions/_shared/replay_protection.ts`:

- Janela default: 600s (10min)
- Aceita timestamp em segundos, ms ou ISO-8601
- Headers detectados por plataforma:
  - Shopify: `X-Shopify-Webhook-Triggered-At` (ISO-8601)
  - Bling: `X-Bling-Event-Timestamp` (epoch)
  - Melhor Envio: `X-MelhorEnvio-Timestamp`
  - Shopee: `X-Shopee-Timestamp`
  - MP: extrai do header `X-Signature` (`ts=...`)
- Em modo dev (sem secret), apenas loga warning. Em prod, rejeita 401.

Ver [[../05-Decisoes/004-replay-protection-10min|ADR-004]].

### Validação de input com Zod

Helper em `supabase/functions/_shared/validation.ts`:

- Schemas reutilizáveis: `zUUID`, `zMoneyCents`, `zQuantity`, `zCEP`, etc.
- Schemas compostos para endpoints críticos
- Helper `validateBody(body, schema, corsHeaders)` retorna 400 com erro estruturado

### Criptografia de credenciais

Helper em `supabase/functions/_shared/encryption.ts` — duas versões coexistindo:

- **v1 (legado):** `base64(IV(12) ‖ ciphertext+tag)` com chave AES-GCM derivada por padding zero
- **v2 (atual):** prefixo `v2:` + base64. Chave derivada via HKDF-SHA256 com salt fixo

Migração lazy: linhas v1 lidas com sucesso são re-encriptadas como v2 e atualizam `crypto_version`.

`encryptValue` SEMPRE produz v2.

Ver [[../05-Decisoes/003-aes-gcm-v2-hkdf-tokens|ADR-003]].

### RLS multi-tenant

- **TODAS as tabelas têm RLS habilitado**
- Policies usam `get_user_org_ids(auth.uid())` para filtrar por org
- `super_admin` (YNTHEGRA) tem acesso total via função `is_super_admin()` SECURITY DEFINER

### Idempotência em pagamentos

- Frontend gera `crypto.randomUUID()` por tentativa de checkout
- Propagado via hooks `useCreatePurchase`, `useCreatePaymentBatch`
- Backend faz lookup ANTES de criar (dedupe em duplo-clique/retry)
- UNIQUE constraint em `purchase_orders.idempotency_key` e `payment_batches.idempotency_key`
- Usado como `external_reference` no Mercado Pago

### Wallet locks

`pg_advisory_xact_lock` durante batch approvals. Evita race condition em débito de carteira concorrente.

### Audit log

Tabela `audit_log` com:
- `correlation_id` (UUID por fluxo de negócio)
- `trace_id` (UUID por request)
- Helper: `_shared/audit.ts`
- Entries em: `create-payment-batch`, `mp-webhook` (approve/reject/cancel), `wallet_debit_for_order`, `shopify_oauth_connected`

---

## 13. Lessons Learned

> Detalhes de cada lição em [[../07-Lessons-Learned/]].

### DNS

- **Hostinger DNS é LENTO** internamente. UI mostra valor novo, mas nameserver authoritative (`byte.dns-parking.com` / `pixel.dns-parking.com`) pode demorar **30-60 minutos** pra propagar mesmo com TTL 300. Ver [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]].
- **Cloudflare 1.1.1.1 é mais agressivo** com cache (TTL respeitado quase ao máximo). Google 8.8.8.8 e Quad9 9.9.9.9 atualizam mais rápido.
- **Mudar nameservers** pra Hostinger nativos resolve qualquer problema de "domain linked to another Vercel account" (libera o domínio do limbo).
- **Vercel detecta IP range expansion** (`216.198.79.1`) mas o IP antigo `76.76.21.21` continua funcionando — não precisa atualizar.

### Supabase

- **Rate limit de email:** 3-4/h no Free tier. Bloqueia signups depois de poucas tentativas. **Solução:** ativar `mailer_autoconfirm: true` via Management API ou configurar SMTP custom (Resend gratuito tem 100/dia). Ver [[../07-Lessons-Learned/Supabase-rate-limit-email-3-por-hora]].
- **API key formats novos:** `sb_publishable_*` (anon) e `sb_secret_*` (service_role) substituem o JWT antigo `eyJ...`. Funcionam com `@supabase/supabase-js >= 2.40`.
- **API admin (`/auth/v1/admin/*`)** ainda exige JWT antigo (não aceita `sb_secret_*` em todos os endpoints). Workaround: usar Management API com PAT pra rodar SQL direto em `auth.users`.
- **`auth.users.confirmed_at` é coluna gerada** — não pode UPDATE direto. Atualizar `email_confirmed_at` resolve. Ver [[../07-Lessons-Learned/Supabase-confirmed-at-eh-coluna-gerada]].
- **Setar senha via SQL:** `UPDATE auth.users SET encrypted_password = crypt('senha', gen_salt('bf'))` (extensão pgcrypto já habilitada).
- **`supabase login` exige TTY interativo** — não roda em ambiente non-interactive. Usar `SUPABASE_ACCESS_TOKEN` env var.
- **`supabase db push` pede senha do banco** — passar via env `SUPABASE_DB_PASSWORD`.
- **`supabase functions deploy` sem args deploya TODAS** as functions de uma vez (~10min para 38).
- **Vercel CLI funciona via npm, Supabase CLI não** — Supabase exige binário nativo. Ver [[../07-Lessons-Learned/Vercel-CLI-via-npm-funciona-Supabase-CLI-nao]].

### Vercel

- **Mesma sessão de browser não permite 2 contas** — tem que usar abas anônimas pra alternar. Ver [[../07-Lessons-Learned/Vercel-multi-account-mesma-sessao-impossivel]].
- **Adicionar membro custa US$ 20/mês recorrente no plano Pro** — não vale a pena pra MVP. Caminho gratuito: criar conta nova com GitHub OAuth e importar o repo.
- **"This domain is linked to another Vercel account"** = remover o domínio do projeto antigo (na conta antiga) PRIMEIRO. Aí o domínio vira "DEPLOYMENT_NOT_FOUND" no Vercel global e pode ser re-adicionado em outra conta. Ver [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]].
- **Auto-deploy bloqueado** ("Implantação bloqueada porque {user} não possui conta Vercel vinculada à conta GitHub") = o GitHub user que pushou commit precisa ter conta Vercel. Solução: criar conta Vercel com mesma conta GitHub.
- **Loop de redirect entre apex e www** = ambos configurados como redirect. Solução: apex = "Connect to environment Production", www = "Redirect 307 → apex".

### Shopify

- **App "Custom" (privado por loja) vs "Public" (multi-loja)** — preferir Public com Custom Distribution: pode escalar pra múltiplos fornecedores sem revisão Shopify. Ver [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]].
- **API version 2026-04** (atual) vs `2024-01` (default no código) — funcionam compatíveis. Não bloqueia.
- **Domínio Shopify interno** sempre `xxx.myshopify.com` — diferente do domínio público que o cliente vê (`ferstex.com.br`).
- **Token rotaciona em cada refresh falho** — não clicar refresh em loop. Ver [[../07-Lessons-Learned/Shopify-token-rotaciona-cada-refresh-falho]].

### GitHub

- **gh CLI não está instalada** no Windows do Rian. Operações via web direta.
- **Branch protection não configurada** no `main` ainda — qualquer push direto funciona.

### Lovable (legado)

- Lovable Cloud cria Supabase **na própria org Lovable** — quando você "sai" do Lovable, perde acesso ao Supabase. Ver [[../07-Lessons-Learned/Lovable-cria-Supabase-em-org-deles]].
- Lovable nomeia env vars em PT (`ID_DO_PROJETO_SUPABASE_VITE`) — manter o nome pra não quebrar código.

### Auth (gerais)

- **Email + GitHub OAuth no mesmo provider Supabase**: se conta tem email vinculado ao GitHub, reset de senha não funciona (Supabase manda email dizendo "use GitHub login").
- **GitHub OAuth do Supabase** está ligado à conta `a.ordem.projeto@gmail.com`. Pra entrar no Dashboard Supabase, fazer "Continue with GitHub" com `aordemprojeto`.

---

## 14. TODOs e Pendências Conhecidas

### Críticos (bloqueiam demo do Stenio)

- [ ] **Conectar loja Shopify do Stenio** (precisa do `xxx.myshopify.com` da loja)
- [ ] **OAuth Bling** pelo painel admin Ferstex (Stenio reconecta)
- [ ] **OAuth Melhor Envio** pelo painel admin Ferstex
- [ ] **Cadastrar 5-10 produtos Ferstex com SKU** que casa com a loja Shopify
- [ ] **Smoke test E2E**: comprar na loja → ver pedido no SISTEX → gerar etiqueta

### Importantes (pré-go-live oficial)

- [ ] **Tela "Alterar senha"** no frontend (Stenio/Erick precisam mudar autonomamente)
- [ ] **Refresh do `www.portalsistex.com`** (não-crítico, mas estética)
- [ ] **Trocar senha `Sistex2026!`** do user `a.ordem.projeto@gmail.com` (foi compartilhada em chat)
- [ ] **Trocar senha do user `rianfort916@gmail.com`** (também compartilhada em chat)
- [ ] **Rotacionar Service Role Key e PAT do Supabase** (foram compartilhados em chat)
- [ ] **Apagar projeto Vercel antigo** na org `synthegra` (Ferstex)
- [ ] **Apagar projeto Supabase antigo** `ngeigfbffuqsviruhrmc` (Lovable Cloud)
- [ ] **Apagar arquivo** `C:/Users/rianf/Documents/YNTHEGRA/sistex-secrets-PRODUCAO.txt` depois de confirmar tudo no 1Password

### Médios (próximos sprints)

- [ ] Onboarding wizard de revendedor não deve aparecer pra super_admin / supplier_admin
- [ ] Templates de email transacional PT-BR
- [ ] Contrato YNTHEGRA × Ferstex (com advogado SaaS)
- [ ] Sentry / PostHog / Plausible

### Estratégicos (médio prazo)

- [ ] Migrar Vercel pra **org YNTHEGRA própria** (criar Team paga depois) ao invés de conta pessoal `aordemprojeto-5068`
- [ ] Bloco 13 — white-label multi-tenant
- [ ] Bloco 14 — sistema de convite de revendedor
- [ ] ALAVANCA 2 — publicação 1-clique em marketplaces

---

## 15. Comandos e Scripts Úteis

> Procedimentos completos em [[../06-Runbooks/]].

### Setup do ambiente local (Windows)

```powershell
# Supabase CLI (instalado em C:\Users\rianf\bin\supabase.exe — adicionado ao PATH)
$env:SUPABASE_ACCESS_TOKEN = "<PAT-no-1password>"
supabase --version

# Vercel CLI (instalado via npm global)
vercel --version
```

### Operações Supabase

```powershell
# Linkar projeto local
$env:SUPABASE_ACCESS_TOKEN = "<PAT>"
supabase --workdir C:\Users\rianf\projetos\sistex link --project-ref eppxxuostbtkuuftgasp --yes

# Aplicar migrations
$env:SUPABASE_DB_PASSWORD = "<DB-password>"
supabase --workdir C:\Users\rianf\projetos\sistex db push --include-all --yes

# Deploy de TODAS edge functions
supabase --workdir C:\Users\rianf\projetos\sistex functions deploy

# Deploy de UMA function específica
supabase --workdir C:\Users\rianf\projetos\sistex functions deploy shopify-webhooks

# Setar secrets
supabase --workdir C:\Users\rianf\projetos\sistex secrets set "FOO=bar" "BAZ=qux"

# Listar secrets (mostra digest, não valor)
supabase --workdir C:\Users\rianf\projetos\sistex secrets list
```

Runbooks: [[../06-Runbooks/Aplicar-Migrations-Supabase]], [[../06-Runbooks/Deploy-EdgeFunction]], [[../06-Runbooks/Setar-Secret-EdgeFunction]].

### Smoke test webhook Shopify

```powershell
$env:SHOPIFY_CLIENT_SECRET = "<secret-real-do-app>"
node C:\Users\rianf\projetos\sistex\scripts\smoke-test-shopify-webhook.mjs
```

Esperado:
```
✅ Test 1: HMAC válido → 200
✅ Test 2: HMAC inválido → 401
✅ Test 3: Replay (timestamp antigo) → 401
```

Runbook: [[../06-Runbooks/Smoke-Test-Webhook-Shopify]].

### Rodar SQL via Management API (sem precisar do DB password)

```powershell
$pat = "<PAT>"
$projectRef = "eppxxuostbtkuuftgasp"
$body = @{ query = "SELECT email, role FROM auth.users LIMIT 5;" } | ConvertTo-Json
Invoke-RestMethod -Method POST `
  -Uri "https://api.supabase.com/v1/projects/$projectRef/database/query" `
  -Headers @{ "Authorization" = "Bearer $pat"; "Content-Type" = "application/json" } `
  -Body $body
```

Runbooks: [[../06-Runbooks/Resetar-Senha-User-via-SQL]], [[../06-Runbooks/Promover-User-SuperAdmin]].

### DNS lookups úteis

```powershell
# Checar nameservers
Resolve-DnsName -Name "portalsistex.com" -Type NS -Server 1.1.1.1

# Checar TXT direto no authoritative
Resolve-DnsName -Name "_vercel.portalsistex.com" -Type TXT -Server byte.dns-parking.com

# Limpar cache DNS local
ipconfig /flushdns
```

### Validar status do site

```powershell
# HTTP HEAD
curl -sI "https://portalsistex.com" --max-time 10

# Verificar que JavaScript aponta pro Supabase certo
$asset = (curl -sL "https://portalsistex.com" | Select-String -Pattern 'assets/index-[^"]*\.js').Matches[0].Value
curl -sL "https://portalsistex.com/$asset" | Select-String "supabase.co"
```

### Git workflow

```bash
# Sempre criar commits semânticos em PT
git commit -m "$(cat <<'EOF'
feat: nova feature de X

Descrição mais detalhada se necessário.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"

# Push (Vercel deploya automático)
git push origin main
```

---

## 16. Protocolo de Handoff Rian ↔ Erick

Quando um deles diz **"fecha o dia"** / **"resumo da sessão"** / **"handoff"** / **"pra eu/Erick assumir"** no fim de uma sessão de trabalho operacional, gerar relatório estruturado:

1. **O que foi feito** — lista cronológica de mudanças concretas (commits, secrets setados, env vars, deploys, registros DB criados, etc.) com IDs/refs quando aplicável
2. **Por quê** — o motivo de cada mudança (resposta a qual demanda/problema/objetivo)
3. **Problemas encontrados e soluções aplicadas** — o que travou, como contornou, decisões tomadas
4. **Estado atual** — o que está funcionando, o que está pendente, o que requer atenção do próximo
5. **Próximos passos sugeridos** — ações claras pra quem assumir, em ordem de prioridade
6. **Pontos de risco / atenção** — algo que pode quebrar, credenciais que vazaram, decisões temporárias que precisam ser revertidas

**Formato:**
- Markdown denso mas escaneável (tabelas, listas, seções)
- Inclui IDs/URLs/commit hashes concretos
- Para sessões grandes: gerar HTML salvável como PDF
- Para sessões menores: markdown limpo no chat
- Sempre incluir "estado das credenciais" (criadas/rotacionadas/expostas)

Sessões registradas em [[../04-Sessoes/]].

---

## 17. Regras Críticas

### NUNCA

- ❌ **Expor `service_role_key` no frontend.** Ele só vive em Edge Functions e env do servidor.
- ❌ **Aceitar webhook sem validação HMAC** em produção.
- ❌ **Interpolar strings diretamente em queries PostgREST** (usar `eq()`, `in()`, `match()`).
- ❌ **Commitar secrets, tokens, senhas** no Git.
- ❌ **Quebrar fluxos existentes de pagamento** sem migração defensiva.
- ❌ **Colocar infra em conta de fornecedor** (Ferstex, Stenio). Sempre YNTHEGRA.
- ❌ **Trocar `ENCRYPTION_MASTER_KEY` depois de tokens estarem cifrados** sem migração — perde acesso aos tokens existentes.
- ❌ **Skip hooks** em commits (`--no-verify`).
- ❌ **`git push --force` em main** sem aviso explícito.

### SEMPRE

- ✅ **Manter backward compatibility total** em qualquer refactor.
- ✅ **Adicionar testes unitários** para helpers novos em `_shared/`.
- ✅ **`tsc --noEmit`** antes de commitar (ou deixar Vercel falhar e corrigir).
- ✅ **Commit messages descritivos em português** com prefixo semântico.
- ✅ **Commits atômicos** (1 tema por commit).
- ✅ **Verificar RLS** ao criar novas tabelas.
- ✅ **Validar HMAC + replay** em todos os webhooks.
- ✅ **Criptografar tokens OAuth** com AES-GCM v2 (HKDF) ao salvar.
- ✅ **`org_id` em toda tabela** que tem dados operacionais.
- ✅ **Audit log** em operações financeiras / state transitions críticos.

---

## 📎 Anexos

### Arquivos relevantes em `C:/Users/rianf/Documents/YNTHEGRA/`

```
precificacao-sistex.html              — Pricing v1
precificacao-sistex-v2.html           — Pricing v2 (final, McKinsey-style)
status-sistex-ferstex.html            — Apresentação reunião Stenio (28/abr)
dia1-checklist-secrets.html           — Checklist Dia 1
migracao-supabase-fase1.html          — Plano migração Supabase
sistex-secrets-PRODUCAO.txt           — APAGAR depois de salvar no 1Password
SISTEX-CLAUDE-MASTER.md               — Original (manter como backup até 1ª revisão completa do vault)
```

### Repo local

```
C:\Users\rianf\projetos\sistex\
├── CLAUDE.md                          — Versão dentro do repo (auto-load Claude Code)
├── src/                               — Frontend
├── supabase/                          — Backend (functions + migrations)
└── scripts/smoke-test-shopify-webhook.mjs
```

---

## 🔄 Como manter este documento atualizado

**Quando Claude (em qualquer sessão) deve atualizar este arquivo:**

- Após mudança de credencial (URL, ID, secret nome)
- Após mudança de arquitetura (nova integração, mudança de provider)
- Após decisão estratégica (modelo de pricing, posicionamento)
- Após problema crítico resolvido (lessons learned)
- Após mudança de status do MVP (concluiu fase, etc.)

**Quando NÃO atualizar:**
- Mudanças triviais (commits de bugfix simples)
- Estado transitório (algo que vai mudar nos próximos minutos)

**Como atualizar:**
- Editar a seção apropriada
- Atualizar `updated:` no frontmatter e a data no topo
- Manter sumário no início conciso

---

> [!success] Próximo Claude que ler este documento
> Você tem 100% do contexto. Não precisa recomeçar do zero.
> Operadores humanos: Rian (`a.ordem.projeto@gmail.com`) e Erick (`erickluisblitz@gmail.com`).
> Demo crítica: terça 05/05/2026 com Stenio (Ferstex).
> Fluxo crítico: pedido Shopify → SISTEX → etiqueta Melhor Envio → Motor de Produção.

**Boa operação.** 🎯
