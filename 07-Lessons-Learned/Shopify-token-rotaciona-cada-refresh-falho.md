---
title: "Lesson — Shopify rotaciona token a cada refresh, não clicar refresh em loop"
tags:
  - lesson-learned
  - shopify
  - oauth
  - tokens
created: 2026-04-30
updated: 2026-05-01
status: ativo
severidade: media
data-incidente: 2026-04-30
---

# 💡 Lesson Learned: Shopify rotaciona token a cada refresh, não clicar refresh em loop

> [!danger] Severidade: **média** (pode invalidar conexão se loop)
> Quando aconteceu: **2026-04-30**, durante setup do app Shopify Partners.

## 🐞 Problema enfrentado

**Sintoma:**
Ao tentar refrescar o Client Secret do app Shopify no Partners Dashboard "só pra ver de novo", o secret antigo foi **invalidado imediatamente**, sem aviso. Qualquer integração que usasse o secret antigo passaria a falhar.

**Impacto:**
Quase quebramos a integração antes mesmo de conectar a primeira loja. No nosso caso o secret novo foi setado nos secrets do Supabase a tempo, mas o reflexo automático foi instintivo (clicar de novo pra "ver outra vez") e quase causou inconsistência.

**Como descobrimos:**
Lendo a documentação Shopify após perceber que o valor exibido era diferente do anterior.

## 🔍 Causa raiz

> [!important] "Refresh secret" no Shopify Partners é "rotacionar secret"
> Cada clique em "Refresh" gera um secret novo e invalida o anterior. **Não há cópia silenciosa**, é uma rotação destrutiva.

**Por que aconteceu:**
- UX da Shopify usa "Refresh" para significar "rotate" — palavra ambígua
- Sem confirmation modal explicando o impacto

**Por que não pegamos antes:**
- Inferência razoável: "refresh" = recarregar a tela. **Errado neste caso.**

## 🛠️ Solução aplicada

**Imediata:** copiar o novo secret e setar nos secrets do Supabase via:
```powershell
supabase --workdir C:\Users\rianf\projetos\sistex secrets set "SHOPIFY_CLIENT_SECRET=<novo-secret>"
```

**Validação:** rodar smoke test webhook Shopify (ver [[../06-Runbooks/Smoke-Test-Webhook-Shopify]]) com o secret novo.

## 🛡️ Prevenção futura

- [x] **Nunca clicar "Refresh" no secret a menos que a intenção seja rotacionar** e ter o plano de propagação pronto
- [ ] Salvar o Client Secret no 1Password imediatamente após criação — **só pode ver uma vez no Partners**
- [x] Documentar no runbook [[../06-Runbooks/Setar-Secret-EdgeFunction]] que rotação Shopify exige update sincronizado em Supabase secrets
- [ ] Adicionar checklist pre-rotação: (1) tenants impactados, (2) downtime aceitável, (3) plano de propagação

## 📚 O que aprendemos

> Em UX de SaaS, "Refresh secret" é convenção comum para "rotacionar com invalidação imediata" — vale para Shopify, GitHub PATs, AWS access keys. **Tratar como ação destrutiva.**
>
> **Imediatamente** após criar credencial em painel third-party, salvar no 1Password — muitos só mostram uma vez.

## 🔗 Relacionado

- [[../05-Decisoes/006-shopify-readonly-scopes|ADR-006]]
- [[../05-Decisoes/007-app-shopify-public-custom-distribution|ADR-007]]
- [[../06-Runbooks/Setar-Secret-EdgeFunction]]
- [[../06-Runbooks/Smoke-Test-Webhook-Shopify]]
- [[../01-Contexto/Infraestrutura]]
