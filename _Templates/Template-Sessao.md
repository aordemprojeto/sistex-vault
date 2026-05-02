---
title: "Sessão <% tp.date.now(\"YYYY-MM-DD\") %> — {{operador}}"
tags:
  - sessao
  - handoff
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
operador: "{{rian-ou-erick}}"
duracao: "{{horas}}"
sprint: "[[02-Sprints/{{sprint-ativa}}]]"
---

# 📅 Sessão — <% tp.date.now("YYYY-MM-DD") %> ({{operador}})

> [!info] Início — <% tp.date.now("HH:mm") %>
> Sprint ativa: [[02-Sprints/{{sprint-ativa}}]]
> Sessão anterior: [[04-Sessoes/{{data-anterior}}-{{operador-anterior}}]]

## ✅ O que foi feito

- {{tarefa-concluida-1}}
- {{tarefa-concluida-2}}

## 🐞 Problemas encontrados

### {{problema-1}}
- **Sintoma:** {{o-que-aconteceu}}
- **Status:** resolvido | em-aberto | bloqueado

## 💡 Soluções aplicadas

- {{solucao-1}} → resolveu {{problema}}

> [!tip] Lesson Learned candidato
> Se algum problema teve causa raiz não-óbvia, criar nota em [[07-Lessons-Learned/]].

## 📦 Artefatos gerados

- Commits: {{hash-1}}, {{hash-2}}
- PRs: {{numero-pr}}
- Documentos: [[link]]
- ADRs: [[05-Decisoes/{{numero-adr}}]]

## 🤝 Handoff para próximo operador

> [!important] Próximo operador: **{{quem-assume}}**

### Estado atual
{{onde-paramos-em-uma-frase}}

### Próximas ações sugeridas
- [ ] {{acao-1}}
- [ ] {{acao-2}}

### Bloqueios e atenção
- ⚠️ {{algo-que-o-proximo-precisa-saber}}

### Comandos prontos para retomar
```bash
{{comandos-uteis}}
```

## 📝 Notas livres

{{contexto-extra}}

## 🔗 Relacionado

- [[02-Sprints/{{sprint-ativa}}]]
- [[04-Sessoes/]] (todas as sessões)
