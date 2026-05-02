---
title: _Templates
tags:
  - readme
created: 2026-05-01
updated: 2026-05-01
---

# _Templates

Modelos para criação de novas notas. Configure o **Obsidian Templater plugin** para apontar para esta pasta.

## Convenções

- Frontmatter padrão: `tags`, `created`, `updated`, `status` (quando aplicável).
- `<% tp.date.now("YYYY-MM-DD") %>` — preenchido automaticamente pelo Templater.
- `{{placeholder}}` — campo manual, substituir ao criar a nota.

## Templates disponíveis

- [[Template-Sprint]] — iniciativas com prazo
- [[Template-Tenant]] — clientes (suppliers/resellers)
- [[Template-Sessao]] — log diário e handoff
- [[Template-ADR]] — decisões arquiteturais
- [[Template-Runbook]] — procedimentos operacionais
- [[Template-Reuniao]] — atas
- [[Template-Lesson-Learned]] — aprendizados com causa raiz
- [[Template-Referencia]] — docs externas curadas
