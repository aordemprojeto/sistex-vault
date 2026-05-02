---
tags:
  - runbook
  - setup
  - onboarding
  - git
  - obsidian
created: 2026-05-02
updated: 2026-05-02
status: ativo
---

# Setup inicial — Novo membro YNTHEGRA (Erick e futuros)

> **Quando usar:** quando um novo membro da equipe YNTHEGRA precisa acessar o vault Obsidian de operação interna no PC dele.
>
> **Tempo estimado:** 30 minutos (uma vez só).

## Pré-requisitos

- [ ] Conta GitHub do novo membro (ex: erickluisblitz)
- [ ] Convite de collaborator no repo `aordemprojeto/sistex-vault` aceito (Rian convida via Settings → Access → Add people com role "Write")
- [ ] Windows 10 ou superior

## Passo 1 — Instalar Git for Windows (5 min)

1. Baixa em https://git-scm.com/download/win
2. Roda o instalador
3. Em todas as telas, deixa as opções **default** (next-next-finish)
4. Pra confirmar instalou: abre PowerShell e roda `git --version` — deve aparecer algo tipo `git version 2.45.x`

## Passo 2 — Instalar GitHub Desktop (3 min)

1. Baixa em https://desktop.github.com/
2. Roda o instalador
3. Quando abrir, clica em **"Sign in to GitHub.com"**
4. Vai abrir o navegador — autentica com sua conta GitHub
5. Volta no GitHub Desktop, configura:
   - **Name:** Erick (YNTHEGRA)
   - **Email:** erickluisblitz@gmail.com
6. Confirma e finaliza setup

## Passo 3 — Clonar o vault (2 min)

1. No GitHub Desktop, clica em **"Clone a repository from the Internet"**
2. Aba **"GitHub.com"** — vai aparecer lista dos repos que você tem acesso
3. Procura **`aordemprojeto/sistex-vault`** e seleciona
4. **Local path:** `C:\Users\<seu-usuario>\Documents\YNTHEGRA`
5. Clica **"Clone"**
6. Em alguns segundos, a pasta YNTHEGRA aparece no Documents com todos os arquivos

## Passo 4 — Instalar Obsidian (2 min)

1. Baixa em https://obsidian.md/
2. Roda o instalador
3. Quando abrir, clica em **"Open folder as vault"**
4. Seleciona `C:\Users\<seu-usuario>\Documents\YNTHEGRA`
5. Confirma "Trust author and enable plugins"

## Passo 5 — Habilitar e configurar plugin Git no Obsidian (10 min)

1. Abre **Settings** (engrenagem no canto inferior esquerdo)
2. Vai em **Plugins da comunidade** → **Turn on community plugins** (se aparecer aviso)
3. Clica em **Browse** e busca por **Git** (autor: Vinzent03)
4. Clica **Install** → **Enable**
5. Volta em Settings e clica em **Git** na sidebar (item novo)
6. Configura **Author** (importante diferenciar dos commits do Rian):
   - **Author name for commit:** `Erick (YNTHEGRA)`
   - **Author email for commit:** `erickluisblitz@gmail.com`
7. Configura intervalos automáticos na seção **Automatic**:
   - Auto commit-and-sync interval: **5**
   - Auto pull interval: **10**
   - Auto commit-and-sync after stopping file edits: **ligar**
8. Configura na seção **Pull**:
   - Pull on startup: **ligar**
9. Configura na seção **Commit message**:
   - List filenames affected by commit in the commit body: **ligar**
10. Configura na seção **History view**:
    - Show Author: **Full**
    - Show Date: **ligar**
11. Reinicia o Obsidian (fecha e abre)

## Passo 6 — Validar funcionamento (3 min)

1. No Obsidian, clica no ícone do Git no sidebar esquerdo
2. Deve aparecer painel "Source control" com **0 changes** e **0 staged**
3. No canto inferior direito do Obsidian, deve aparecer status `Git: main` com checkmark verde
4. Cria uma nota teste em `00-Inbox/teste-setup.md` com texto qualquer
5. Salva (Ctrl+S no Obsidian)
6. Aguarda 5 minutos OU força commit manual:
   - Clica **+** (stage all) no painel Git
   - Clica **✓** (commit)
   - Clica **↑** (push)
7. Confirma que o commit aparece em https://github.com/aordemprojeto/sistex-vault/commits/main com seu nome (Erick)
8. Apaga a nota teste, salva, deixa o auto-commit rodar pra limpar

## Passo 7 — Primeira leitura recomendada

Depois de tudo configurado, abre essas notas no Obsidian na ordem (5 min):

1. **MOC.md** — índice navegável de todo o vault
2. **01-Contexto/CLAUDE.md** — contexto mestre do projeto SISTEX
3. **04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian.md** — handoff da última sessão grande
4. **02-Sprints/MVP-Stenio-05mai.md** — sprint atual

Depois disso, você está com 100% do contexto pra operar.

## Troubleshooting

### "Permission denied" ao clonar

- Confirma que o convite de collaborator do GitHub foi aceito
- Confirma que está logado no GitHub Desktop com a conta correta

### Plugin Git pede autenticação ao primeiro push

- Vai abrir popup do navegador
- Autentica com a conta GitHub do membro (NÃO confunde com conta de outro)
- Depois disso, fica salvo no Git Credential Manager

### Conflito de commits ao fazer pull

- Acontece se você editou um arquivo que outro membro também editou
- O plugin avisa e mostra o conflito
- Resolve manualmente abrindo o arquivo e escolhendo qual versão fica

## Notas finais

- **NÃO compartilhe credenciais com outros membros** — cada um deve ter sua própria conta GitHub
- **NÃO commite arquivos sensíveis** — o `.gitignore` já filtra `.env`, `*.key`, `sistex-secrets-PRODUCAO.txt`, etc.
- **Sempre faz "Pull" ao começar a trabalhar** (se não estiver com Pull on startup ligado) — pra ter a versão mais recente
- **Quando criar/editar muitas notas em uma sessão**, força um Commit + Push manual no fim — não espera os 5 minutos do auto-commit

## Links relacionados

- [[Setar-Secret-EdgeFunction]]
- [[Conectar-Shopify-Tenant]]
- [[../01-Contexto/CLAUDE|CLAUDE.md - contexto mestre]]
- [[../04-Sessoes/2026-05-01-migracao-completa-e-vault-obsidian|Sessão 2026-05-01]]
