---
title: MVP-Stenio-05mai
tags:
  - sprint
  - mvp
  - ferstex
created: 2026-05-01
updated: 2026-05-01
status: in_progress
prazo: 2026-05-05
sponsor: Stenio (Ferstex)
operador: rian-e-erick
---

# 🎯 Sprint: MVP-Stenio-05mai

## 🎯 Objetivo

Entregar **MVP demonstrável** do fluxo Shopify → SISTEX → Etiqueta Melhor Envio → Motor de Produção, rodando ao vivo com a loja Shopify do Stenio durante a demo.

> [!important] Demo crítica — terça **2026-05-05**
> Stenio (dono da Ferstex) estava desanimado com a demora. Reunião 2026-04-28 alinhou expectativa: **não é lançamento oficial**, só o fluxo principal funcionando uma vez. Se rodar uma vez na demo, é sucesso. Ver [[../03-Tenants/Ferstex/Reunioes/2026-04-28-alinhamento-prazo|ata]].

## 🚦 Fluxo único alvo

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

## 📦 Deliverables

### Críticos (bloqueiam demo)

- [x] **Migração Supabase** para conta YNTHEGRA própria (`eppxxuostbtkuuftgasp`)
- [x] **Migração Vercel** para conta `aordemprojeto-5068` + DNS Hostinger
- [x] **Smoke test webhook Shopify** 3/3 passando
- [x] **App Shopify Partners** criado, Client ID/Secret setados
- [x] **Org YNTHEGRA + Org Ferstex** criadas; user Rian como super_admin + supplier_admin
- [x] **Auto-confirm de email** ativado (próximos signups sem rate limit)
- [ ] **Conectar loja Shopify do Stenio** — depende do `xxx.myshopify.com` da loja (pendente Stenio)
- [ ] **OAuth Bling** pelo painel admin Ferstex (Stenio reconecta)
- [ ] **OAuth Melhor Envio** pelo painel admin Ferstex (Stenio reconecta)
- [ ] **Cadastrar 5-10 produtos Ferstex com SKU** que casam com loja Shopify
- [ ] **Smoke test E2E manual** — compra → pedido → etiqueta → relatório

### Importantes (pré-demo, mas não bloqueiam)

- [ ] **Tela "Alterar senha"** no frontend (Stenio precisa mudar autonomamente)
- [ ] **Trocar senha** `Sistex2026!` do user `a.ordem.projeto@gmail.com` (vazou em chat)

## 📅 Plano dia-a-dia

| Dia | Data | Status | Tarefa |
|---|---|---|---|
| Dia 1 | 2026-05-01 | ✅ | Migração Supabase + Vercel + DNS + Smoke test + App Shopify |
| Dia 2 | 2026-05-02 | ⏳ | Conectar loja Shopify do Stenio + OAuth Bling/ME |
| Dia 3 | 2026-05-03 | ⏳ | Cadastrar produtos Ferstex + SKU matching |
| Dia 4 | 2026-05-04 | ⏳ | Smoke test E2E manual: compra → etiqueta → Motor de Produção |
| Dia 5 | 2026-05-05 | ⏳ | **Demo ao vivo com Stenio** |

## 🔗 Dependências

- 🟡 **Stenio fornece domínio `xxx.myshopify.com`** da loja dele
- 🟡 **Stenio reconecta Bling e Melhor Envio** com credenciais Ferstex
- 🟢 Acesso Supabase via PAT — OK
- 🟢 Acesso Vercel via dashboard — OK
- 🟢 App Shopify Partners criado — OK

## 📊 Métricas de sucesso

- ✅ Demo rodou o fluxo único **uma vez** ao vivo, com Stenio comprando na própria loja
- ✅ Pedido apareceu no painel SISTEX
- ✅ Etiqueta Melhor Envio gerada com 1 clique
- ✅ Pedido entrou no Motor de Produção (relatório PDF/XLS)

## ⚠️ Riscos

| Risco | Mitigação |
|---|---|
| Stenio não fornecer `xxx.myshopify.com` a tempo | Contato direto via WhatsApp; ter loja Shopify de teste como fallback |
| Bling/ME requererem KYC/aprovação | Iniciar OAuth no Dia 2 (margem para pendências) |
| Senha do super_admin vazada | Tela "Alterar senha" antes da demo OU SQL UPDATE manual |
| DNS `www.portalsistex.com` "Verification Needed" | Não-bloqueante (apex funciona); resolver post-demo |

## 🔗 Relacionado

- Tenant: [[../03-Tenants/Ferstex/README|Ferstex]]
- Reunião que originou: [[../03-Tenants/Ferstex/Reunioes/2026-04-28-alinhamento-prazo]]
- ADRs relevantes:
  - [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005 — infra sempre na YNTHEGRA]]
  - [[../05-Decisoes/006-shopify-readonly-scopes|ADR-006 — Shopify read-only]]
  - [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007 — Shopify Public + Custom]]
  - [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008 — DNS Hostinger nativo]]
- Sessão de origem: [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian]]
- Lessons que afetam a sprint: [[../07-Lessons-Learned/]]

## 🔁 Status updates

### 2026-05-01 (sexta)
- Migração total Supabase + Vercel + DNS concluída em sessão única (~5h)
- Smoke test webhook Shopify: 3/3 passando com secret real
- App Shopify Partners criado, credenciais setadas
- Auth funcionando (Rian logado como super_admin)
- Vault Obsidian criado e populado
- **Próximo passo:** Stenio fornecer `xxx.myshopify.com` para conectar loja
