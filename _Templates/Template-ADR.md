---
title: "ADR-{{NNN}} — {{titulo-curto}}"
tags:
  - adr
  - decisao
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
status: proposed
numero: "{{NNN}}"
autor: "{{rian-ou-erick}}"
supersedes: ""
superseded-by: ""
---

# ADR-{{NNN}} — {{titulo-curto}}

> [!info] Status: **proposed**
> Valores possíveis: `proposed`, `accepted`, `deprecated`, `superseded`.

## 🌐 Contexto

{{qual-problema-nos-trouxe-aqui}}

{{quais-forcas-estao-em-jogo-restricoes-prazos-custos-time}}

## 🎯 Decisão

**Decidimos {{decisao-em-uma-frase}}.**

{{justificativa-detalhada}}

## 🔀 Alternativas consideradas

### Alternativa A — {{nome}}
- **Como funcionaria:** {{descricao}}
- **Prós:** {{lista}}
- **Contras:** {{lista}}
- **Por que não escolhemos:** {{razao}}

### Alternativa B — {{nome}}
- **Como funcionaria:** {{descricao}}
- **Prós:** {{lista}}
- **Contras:** {{lista}}
- **Por que não escolhemos:** {{razao}}

## 📈 Consequências

### Positivas
- {{beneficio-1}}

### Negativas / trade-offs aceitos
- {{custo-1}}

### Riscos
- {{risco-1}} — mitigação: {{como}}

## 🔁 Revisão

- **Quando revisar:** {{condicao-ex-quando-passar-de-X-tenants}}
- **Como saberemos que a decisão foi errada:** {{sinal}}

## 🔗 Relacionado

- ADRs relacionadas: [[05-Decisoes/{{adr-relacionada}}]]
- Sprint: [[02-Sprints/{{sprint}}]]
- Lesson Learned: [[07-Lessons-Learned/{{lesson}}]]
