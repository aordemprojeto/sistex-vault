---
title: "{{nome-da-sprint}}"
tags:
  - sprint
  - "{{tag-tema}}"
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
status: planejada
prazo: "{{YYYY-MM-DD}}"
sponsor: "{{quem-pediu}}"
operador: "{{rian-ou-erick}}"
---

# Sprint: {{nome-da-sprint}}

## 🎯 Objetivo

{{descricao-curta-do-objetivo-em-uma-frase}}

> [!info] Prazo
> **{{YYYY-MM-DD}}** — {{contexto-do-prazo-ex-demo-com-cliente}}

## 📦 Deliverables

- [ ] {{deliverable-1}}
- [ ] {{deliverable-2}}
- [ ] {{deliverable-3}}

## 🔗 Dependências

- {{dependencia-1-ex-acesso-a-supabase-prod}}
- {{dependencia-2}}

## 📊 Métricas de sucesso

Como saberemos que a sprint atingiu o objetivo?

- {{metrica-1-ex-demo-roda-end-to-end-sem-falhas}}
- {{metrica-2-ex-cliente-aprova-proposta-de-precificacao}}

## ⚠️ Riscos

- {{risco-1-e-mitigacao}}

## 📝 Notas

{{notas-soltas}}

## 🔁 Status updates

### <% tp.date.now("YYYY-MM-DD") %>
- {{o-que-mudou}}

## 🔗 Relacionado

- ADRs: [[05-Decisoes/]]
- Sessões: [[04-Sessoes/]]
- Tenant impactado: [[03-Tenants/{{cliente}}/README]]
