---
title: MOC — YNTHEGRA Vault
tags:
  - moc
  - index
created: 2026-05-01
updated: 2026-05-01
---

# 🗺️ Map of Content — YNTHEGRA

Vault do projeto **SISTEX/YNTHEGRA** — SaaS multi-tenant para dropshipping de moda fitness.

> [!info] Operadores
> Rian (`a.ordem.projeto@gmail.com`) e Erick (`erickluisblitz@gmail.com`) — alternam turnos. Handoff via [[04-Sessoes/README|sessões diárias]].

## 📂 Pastas principais

| Pasta | Propósito |
|---|---|
| [[00-Inbox/README\|00-Inbox]] | Captura rápida sem destino definido |
| [[01-Contexto/README\|01-Contexto]] | Mestres lidos primeiro pelo Claude |
| [[02-Sprints/README\|02-Sprints]] | Iniciativas com prazo |
| [[03-Tenants/README\|03-Tenants]] | Um diretório por cliente |
| [[04-Sessoes/README\|04-Sessoes]] | Log diário e handoff entre operadores |
| [[05-Decisoes/README\|05-Decisoes]] | ADRs numerados |
| [[06-Runbooks/README\|06-Runbooks]] | Procedimentos operacionais |
| [[07-Lessons-Learned/README\|07-Lessons-Learned]] | Aprendizados com causa raiz |
| [[08-Reunioes/README\|08-Reunioes]] | Atas (sócios, fornecedores) |
| [[09-Referencias/README\|09-Referencias]] | Docs externas |
| [[10-Documentos-Premium/README\|10-Documentos-Premium]] | PDFs/HTMLs para clientes |
| [[99-Arquivo/README\|99-Arquivo]] | Deprecated, referência histórica |
| [[_Templates/README\|_Templates]] | Modelos com frontmatter |

## 🧠 Contexto mestre

- [[01-Contexto/CLAUDE|CLAUDE.md]] — contexto principal lido primeiro
- [[01-Contexto/Negocio|Negocio]] — modelo de negócio, monetização, mercado
- [[01-Contexto/Arquitetura|Arquitetura]] — stack, decisões técnicas, diagramas
- [[01-Contexto/Infraestrutura|Infraestrutura]] — Supabase, deploys, secrets
- [[01-Contexto/Pessoas|Pessoas]] — sócios, operadores, stakeholders

## 🎯 Sprints ativas

- [[02-Sprints/MVP-Stenio-05mai|MVP-Stenio-05mai]] — demo terça **2026-05-05**
- [[02-Sprints/Bloco-13-Whitelabel|Bloco-13-Whitelabel]]

## 🏷️ Tenants

- [[03-Tenants/Ferstex/README|Ferstex]] — cliente piloto (dono Stenio)

## 📋 Convenções

- Pastas com prefixo numérico (`00-`, `01-`, ...) para ordenação.
- ADRs numerados sequencialmente: `001-titulo.md`, `002-titulo.md`.
- Cada tenant em `03-Tenants/` tem subpasta `Reunioes/` própria.
- Templates em `_Templates/` usam Obsidian Templater (`<% %>`) para dinâmicos e `{{placeholder}}` para campos manuais.
- Frontmatter padrão: `tags`, `created`, `updated`, `status` (quando aplicável).
