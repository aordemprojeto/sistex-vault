---
title: "Reunião <% tp.date.now(\"YYYY-MM-DD\") %> — {{tema}}"
tags:
  - reuniao
  - "{{tag-cliente-ou-socios-ou-fornecedor}}"
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
data: <% tp.date.now("YYYY-MM-DD") %>
duracao: "{{minutos}}"
formato: "{{presencial-online-hibrido}}"
---

# 📅 Reunião — {{tema}}

> [!info] Metadados
> **Data:** <% tp.date.now("YYYY-MM-DD HH:mm") %>
> **Duração:** {{minutos}} min
> **Formato:** {{presencial-online}}

## 👥 Participantes

- {{nome-1}} ({{papel-ex-CEO-Ferstex}})
- {{nome-2}} ({{papel}})
- {{nome-3}}

**Ausentes (esperados):** {{quem-faltou}}

## 📋 Agenda

1. {{item-1}}
2. {{item-2}}
3. {{item-3}}

## 💬 Discussão

### {{topico-1}}
{{resumo-da-discussao}}

### {{topico-2}}
{{resumo}}

## ✅ Decisões tomadas

> [!success] Decisões
> - **{{decisao-1}}** — responsável: {{quem}}
> - **{{decisao-2}}** — responsável: {{quem}}

Decisões que merecem ADR: registrar em [[05-Decisoes/]].

## 🎯 Ações com responsável e prazo

| Ação | Responsável | Prazo | Status |
|---|---|---|---|
| {{acao-1}} | {{nome}} | {{YYYY-MM-DD}} | aberta |
| {{acao-2}} | {{nome}} | {{YYYY-MM-DD}} | aberta |
| {{acao-3}} | {{nome}} | {{YYYY-MM-DD}} | aberta |

## ❓ Questões em aberto

- {{questao-1}}
- {{questao-2}}

## 📝 Notas livres

{{observacoes}}

## 🔗 Relacionado

- Reunião anterior: [[{{link}}]]
- Tenant: [[03-Tenants/{{cliente}}/README]]
- Sprint: [[02-Sprints/{{sprint}}]]
