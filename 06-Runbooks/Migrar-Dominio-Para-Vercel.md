---
title: "Runbook — Migrar domínio para Vercel (com DNS Hostinger)"
tags:
  - runbook
  - vercel
  - dns
  - hostinger
  - dominio
created: 2026-05-01
updated: 2026-05-01
status: ativo
criticidade: alta
ultima-execucao: 2026-04-30
---

# 📖 Runbook: Migrar domínio para Vercel (com DNS Hostinger)

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - Movendo domínio entre contas Vercel
> - Trocando hosting de outro provider para Vercel
> - Saindo de Vercel-managed DNS para Hostinger DNS (princípio do [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]])

> [!important] Esperar tempo de propagação
> Hostinger DNS pode demorar **30-60 minutos** para propagação inicial — ver [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]]. **Não fazer em janela < 1h até deadline crítico.**

## ✅ Pré-requisitos

- [ ] Acesso a Hostinger (registrar/DNS) com conta YNTHEGRA
- [ ] Acesso a Vercel **conta correta** (YNTHEGRA, não cliente)
- [ ] Aba anônima OU perfil de browser separado se houver outra conta Vercel logada — ver [[../07-Lessons-Learned/Vercel-multi-account-mesma-sessao-impossivel]]
- [ ] Repo `YNTHEGRA/sistex` importado na conta Vercel destino

## 🛠️ Passos

### 1. (Se aplicável) Remover domínio da conta Vercel antiga

Logado na conta antiga:
- Vai em **Project → Settings → Domains**
- Remove `portalsistex.com` (e `www.portalsistex.com` se existir)

Imediatamente após:
- Acesso ao domínio retorna `DEPLOYMENT_NOT_FOUND` — esperado, é o estado de "limbo"
- Site fora do ar: aceitar essa janela

### 2. Trocar nameservers no Hostinger (se ainda Vercel-managed)

Logado na Hostinger → **Domains → portalsistex.com → DNS / Nameservers**:

| Antes | Depois |
|---|---|
| `ns1.vercel-dns.com` | `byte.dns-parking.com` |
| `ns2.vercel-dns.com` | `pixel.dns-parking.com` |

Salvar.

> [!info] Por que não Vercel-managed
> [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]] — autoridade DNS na YNTHEGRA, fora do Vercel. Resolve "domain linked to another Vercel account".

### 3. Adicionar domínio na conta Vercel destino

**Aba anônima** se necessário. Logado na conta YNTHEGRA → **Project sistex → Settings → Domains → Add**:

- Adicionar `portalsistex.com` → marca como **Production** (Connect to environment Production)
- Adicionar `www.portalsistex.com` → marca como **Redirect → portalsistex.com (307)**

> [!warning] Não fazer ambos como Production
> Se ambos forem Production sem redirect, gera **loop** entre apex e www.

### 4. Configurar DNS records no Hostinger

Hostinger → **DNS Records**, adicionar:

| Tipo | Nome | Valor | TTL |
|---|---|---|---|
| A | `@` | `76.76.21.21` | 300 |
| CNAME | `www` | `cname.vercel-dns.com` | 300 |
| TXT | `_vercel` | `vc-domain-verify=portalsistex.com,...` (Vercel mostra o valor) | 300 |
| TXT | `_vercel.www` | `vc-domain-verify=www.portalsistex.com,...` | 300 |

> [!info] Vercel pode mostrar IP novo (`216.198.79.1`)
> O IP antigo `76.76.21.21` continua funcionando. Não precisa atualizar — funcionam ambos.

### 5. Aguardar propagação (~30-60 min)

Enquanto isso, monitorar:

```powershell
# Authoritative (mais rápido pra ver mudança)
Resolve-DnsName -Name "portalsistex.com" -Type NS -Server 1.1.1.1
Resolve-DnsName -Name "_vercel.portalsistex.com" -Type TXT -Server byte.dns-parking.com

# Cache local
ipconfig /flushdns
```

### 6. Vercel valida ownership automaticamente

Quando os TXT propagarem, o Vercel sai de "Verification Needed" para ✅ verde. Pode levar até 60min ([[../07-Lessons-Learned/DNS-Hostinger-cache-37min]]).

### 7. Validar acesso ao domínio

```powershell
curl -sI "https://portalsistex.com" --max-time 10
curl -sI "https://www.portalsistex.com" --max-time 10
```

Esperado:
- `https://portalsistex.com` → `200 OK`
- `https://www.portalsistex.com` → `307 Temporary Redirect` para apex

### 8. (Se aplicável) Validar que o site aponta pra backend correto

Útil após migração de Supabase também:

```powershell
$asset = (curl -sL "https://portalsistex.com" | Select-String -Pattern 'assets/index-[^"]*\.js').Matches[0].Value
curl -sL "https://portalsistex.com/$asset" | Select-String "supabase.co"
```

Esperado: aparece `eppxxuostbtkuuftgasp.supabase.co` (o Supabase YNTHEGRA, não o legado Lovable).

## ✔️ Validação final

- [ ] `portalsistex.com` retorna 200
- [ ] `www.portalsistex.com` redireciona 307 para apex
- [ ] Vercel domain status: ✅ verde para ambos
- [ ] Asset JS bundled aponta para Supabase correto
- [ ] Smoke test webhook ([[Smoke-Test-Webhook-Shopify]]) passa

## 🐞 Troubleshooting

### Sintoma: "This domain is linked to another Vercel account"
**Causa:** ainda há referência na conta antiga OU nameserver ainda Vercel-managed.
**Correção:** confirmar passo 1 (remover da antiga) e passo 2 (NS Hostinger).
Ver [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]].

### Sintoma: Loop de redirect entre apex e www
**Causa:** ambos configurados como Production no Vercel.
**Correção:** apex = Production, www = Redirect.

### Sintoma: TXT não propaga depois de 30min
**Causa:** cache interno Hostinger (normal).
**Correção:** esperar até 1h. Ver [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]]. Checar authoritative direto: `Resolve-DnsName -Server byte.dns-parking.com`.

### Sintoma: Auto-deploy bloqueado ("user não possui conta Vercel vinculada")
**Causa:** GitHub user que faz push não tem conta Vercel.
**Correção:** o user que faz push (ex: `aordemprojeto`) precisa ter conta Vercel. Se for um bot ou outra conta, dar acesso na Vercel.

## 📞 Escalar para

- Se DNS não propagar > 2h: suporte Hostinger
- Se Vercel insistir em "linked to another account" mesmo após NS trocados: suporte Vercel (raro)

## 📝 Histórico de execuções

| Data | Operador | Domínio | De → Para | Resultado |
|---|---|---|---|---|
| 2026-04-30 → 2026-05-01 | Rian | portalsistex.com | Vercel `synthegra` (Ferstex) → Vercel `aordemprojeto-5068` (YNTHEGRA) | ✅ ok (37min DNS cache) |

## 🔗 Relacionado

- [[../05-Decisoes/005-infra-sempre-na-ynthegra|ADR-005]]
- [[../05-Decisoes/008-dns-hostinger-nativos-vez-de-vercel-managed|ADR-008]]
- [[../07-Lessons-Learned/DNS-Hostinger-cache-37min]]
- [[../07-Lessons-Learned/Vercel-domain-limbo-quando-removido]]
- [[../07-Lessons-Learned/Vercel-multi-account-mesma-sessao-impossivel]]
- [[Smoke-Test-Webhook-Shopify]]
