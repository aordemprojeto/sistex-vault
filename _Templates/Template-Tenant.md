---
title: "{{nome-do-tenant}}"
tags:
  - tenant
  - "{{tipo-supplier-ou-reseller}}"
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
status: ativo
tipo: "{{supplier-ou-reseller}}"
cnpj: "{{00.000.000/0000-00}}"
mrr: 0
contrato: "{{ativo-piloto-encerrado}}"
onboarded: "{{YYYY-MM-DD}}"
---

# 🏢 {{nome-do-tenant}}

## 📇 Identificação

- **Razão social:** {{razao-social}}
- **Nome fantasia:** {{nome-fantasia}}
- **Tipo:** {{supplier-ou-reseller}}
- **CNPJ:** {{cnpj}}
- **Site:** {{url}}

## 👤 Contato principal

- **Nome:** {{nome-contato}}
- **Cargo:** {{cargo}}
- **Email:** {{email}}
- **Telefone/WhatsApp:** {{telefone}}

## 💰 Comercial

- **MRR:** R$ {{valor}}
- **Plano:** {{plano-contratado}}
- **Início do contrato:** {{YYYY-MM-DD}}
- **Renovação:** {{YYYY-MM-DD}}

## 🔌 Integrações ativas

- [ ] Supabase
- [ ] Shopify
- [ ] Bling
- [ ] Melhor Envio
- [ ] {{outra-integracao}}

## 📋 Status atual

> [!info] Última atualização: <% tp.date.now("YYYY-MM-DD") %>
> {{onde-o-tenant-esta-no-funil-ou-na-operacao}}

## 📂 Documentos

- Contrato: {{link-ou-localizacao}}
- Proposta: {{link}}
- Reuniões: [[Reunioes/]]

## 📝 Histórico

### <% tp.date.now("YYYY-MM-DD") %>
- {{evento}}

## 🔗 Relacionado

- Sprints: [[02-Sprints/]]
- Reuniões: [[Reunioes/]]
