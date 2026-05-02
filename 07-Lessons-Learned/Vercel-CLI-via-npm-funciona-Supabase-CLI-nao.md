---
title: "Lesson — Vercel CLI funciona via npm; Supabase CLI exige binário nativo"
tags:
  - lesson-learned
  - cli
  - supabase
  - vercel
  - windows
created: 2026-04-30
updated: 2026-05-01
status: ativo
severidade: baixa
data-incidente: 2026-04-30
---

# 💡 Lesson Learned: Vercel CLI via `npm install -g` funciona; Supabase CLI **NÃO**

> [!danger] Severidade: **baixa** (perda de tempo)
> Quando aconteceu: **2026-04-30**, durante setup do ambiente local Windows.

## 🐞 Problema enfrentado

**Sintoma:**
`npm install -g supabase` aparentemente sucesso, mas `supabase --version` falhava ou retornava versão muito antiga / inconsistente. Comandos como `supabase db push` quebravam com erros estranhos.

**Impacto:**
Atraso no setup do ambiente local. Tentativas repetidas de reinstalar via `npm` não resolveram.

**Como descobrimos:**
Documentação Supabase CLI deixa claro que **a versão npm é deprecated** — o pacote `supabase-cli` no npm é stub que apenas instrui a baixar o binário nativo.

## 🔍 Causa raiz

> [!important] Supabase CLI exige binário nativo (Go), não wrapper Node
> Funcionalidades que usam libs nativas (autenticação OAuth, deploy de functions Deno) não funcionam pelo wrapper npm. Por isso o time Supabase deprecou e recomenda binário oficial.

**Por que aconteceu:**
- Convenção: muitos CLIs grandes (Vercel, Supabase) começam com versão npm
- Vercel manteve compatibilidade Node; Supabase migrou inteiro pra Go

**Por que não pegamos antes:**
- Reflexo Linux/macOS: "tudo é npm install -g"
- Documentação Supabase enterra esse detalhe em "instalação"

## 🛠️ Solução aplicada

**Baixar binário oficial Supabase CLI manualmente:**

```powershell
# Instalado em C:\Users\rianf\bin\supabase.exe
# Adicionado ao PATH do Windows
$env:PATH += ";C:\Users\rianf\bin"
supabase --version  # OK
```

Para autenticação não-interativa (necessária em Windows pela ausência de TTY confiável em alguns contextos):

```powershell
$env:SUPABASE_ACCESS_TOKEN = "<PAT-no-1password>"
```

**Tempo para resolver:** ~20 minutos (descobrir, baixar, configurar PATH).

## 🛡️ Prevenção futura

- [x] Documentado em [[../01-Contexto/CLAUDE]] seção 15 (Comandos)
- [x] Runbook [[../06-Runbooks/Aplicar-Migrations-Supabase]] usa caminho completo `C:\Users\rianf\bin\supabase.exe` ou assume PATH configurado
- [ ] Ao montar ambiente em outro computador (Erick, futuras máquinas), sempre baixar binário, **não usar npm**

## 📚 O que aprendemos

> "Tem CLI no npm" ≠ "CLI funciona via npm". Verificar versão do pacote npm vs releases oficiais antes de instalar — se npm ficou parado, é stub.
>
> **Supabase CLI requer:**
> - Binário nativo
> - `SUPABASE_ACCESS_TOKEN` env var pra modo não-interativo
> - `SUPABASE_DB_PASSWORD` env var pra `db push`

## 🔗 Relacionado

- [[../06-Runbooks/Aplicar-Migrations-Supabase]]
- [[../06-Runbooks/Deploy-EdgeFunction]]
- [[../06-Runbooks/Setar-Secret-EdgeFunction]]
- [[../01-Contexto/CLAUDE]] — seção Comandos
