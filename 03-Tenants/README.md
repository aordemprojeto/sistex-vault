---
title: 03-Tenants
tags:
  - readme
created: 2026-05-01
updated: 2026-05-01
---

# 03-Tenants

Um diretório por cliente (suppliers ou resellers). Cada tenant tem subpasta `Reunioes/` para atas específicas daquele cliente. Use [[../_Templates/Template-Tenant|Template-Tenant]] como `README.md` da pasta do tenant.

**Estrutura padrão por tenant:**

```
03-Tenants/<Nome>/
├── README.md          ← preenchido com Template-Tenant
├── Reunioes/          ← atas específicas do cliente
└── ...                ← outros docs do tenant
```
