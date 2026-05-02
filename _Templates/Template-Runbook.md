---
title: "Runbook — {{nome-do-procedimento}}"
tags:
  - runbook
  - "{{tag-area-ex-supabase-shopify-deploy}}"
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
status: ativo
criticidade: "{{baixa-media-alta}}"
ultima-execucao: ""
---

# 📖 Runbook: {{nome-do-procedimento}}

## 🚨 Quando usar

> [!warning] Acione este runbook quando:
> - {{gatilho-1-ex-banco-de-um-tenant-fora-do-ar}}
> - {{gatilho-2}}

## ✅ Pré-requisitos

- [ ] {{acesso-1-ex-credencial-supabase-prod}}
- [ ] {{ferramenta-1-ex-cli-instalada}}
- [ ] {{permissao-1}}

## 🛠️ Passos

### 1. {{primeiro-passo}}

{{descricao-do-que-fazer}}

```bash
{{comando-pronto-para-copiar}}
```

**Validação:** {{como-saber-que-o-passo-deu-certo}}

### 2. {{segundo-passo}}

{{descricao}}

```bash
{{comando}}
```

**Validação:** {{como-confirmar}}

### 3. {{terceiro-passo}}

{{descricao}}

## ✔️ Validação final

Confirme que tudo está OK:

- [ ] {{check-1}}
- [ ] {{check-2}}
- [ ] {{check-3-ex-tenant-respondendo-a-requests}}

## 🐞 Troubleshooting

### Sintoma: {{erro-comum-1}}
**Causa provável:** {{razao}}
**Correção:**
```bash
{{comando-de-fix}}
```

### Sintoma: {{erro-comum-2}}
**Causa provável:** {{razao}}
**Correção:** {{passos}}

## 📞 Escalar para

Se nada acima resolver:
- {{quem-chamar-ex-stenio-do-ferstex-via-whatsapp}}

## 📝 Histórico de execuções

| Data | Operador | Resultado | Observações |
|---|---|---|---|
| <% tp.date.now("YYYY-MM-DD") %> | {{nome}} | {{ok-ou-falhou}} | {{notas}} |

## 🔗 Relacionado

- ADR: [[05-Decisoes/{{adr}}]]
- Lesson Learned: [[07-Lessons-Learned/{{lesson}}]]
