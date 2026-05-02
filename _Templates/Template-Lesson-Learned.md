---
title: "Lesson — {{titulo-curto}}"
tags:
  - lesson-learned
  - "{{tag-area}}"
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
status: ativo
severidade: "{{baixa-media-alta-critica}}"
data-incidente: "{{YYYY-MM-DD}}"
---

# 💡 Lesson Learned: {{titulo-curto}}

> [!danger] Severidade: **{{baixa-media-alta-critica}}**
> Quando aconteceu: {{YYYY-MM-DD}}

## 🐞 Problema enfrentado

**Sintoma:**
{{o-que-foi-observado-pelo-usuario-ou-pelo-sistema}}

**Impacto:**
{{quem-foi-afetado-e-como-ex-ferstex-ficou-sem-checkout-por-2h}}

**Como descobrimos:**
{{alerta-cliente-reclamou-monitoria}}

## 🔍 Causa raiz

> [!important] Causa raiz (não o sintoma)
> {{a-causa-fundamental-nao-o-erro-superficial}}

**Por que aconteceu:**
{{cadeia-de-eventos-que-levou-ao-problema}}

**Por que não pegamos antes:**
{{o-que-faltou-em-testes-monitoria-revisao}}

## 🛠️ Solução aplicada

{{o-que-foi-feito-para-resolver}}

```bash
{{comando-ou-codigo-do-fix}}
```

**Tempo para resolver:** {{minutos-ou-horas}}

## 🛡️ Prevenção futura

Como impedir que isso aconteça de novo:

- [ ] {{acao-preventiva-1-ex-adicionar-teste}}
- [ ] {{acao-preventiva-2-ex-criar-runbook}}
- [ ] {{acao-preventiva-3-ex-monitor-com-alerta}}

## 📚 O que aprendemos

{{insight-genuino-que-gostariamos-de-ter-sabido-antes}}

## 🔗 Relacionado

- Sessão onde apareceu: [[04-Sessoes/{{data-operador}}]]
- Runbook criado: [[06-Runbooks/{{runbook}}]]
- ADR resultante: [[05-Decisoes/{{adr}}]]
- Tenant impactado: [[03-Tenants/{{tenant}}/README]]
