---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 17
url: https://github.com/VictorNascimento14/Arcada/pull/17
branch: ui/primitivos
tags: [pr, fundacao, design]
status: merged
---

# PR #17 — ui(primitivos): instalar os primitivos do vidro-orgânico

## 🎯 Contexto

Módulo 0 do [[2026-09-30-plano-da-v1]]. Decisão em [[ADR-002-sistema-visual-vidro-organico]]. Fecha a issue #5.

## 🔧 Mudanças

- `src/ui/base/`, `src/ui/lib/`, `src/ui/hooks/` — cópia do kit vidro-orgânico **já com as correções** do `TextField` (ícone acima do campo) e do `Calendar` ("Setembro de 2026"), que voltaram ao kit em Design-moderno#3 e #4.
- `src/ui/lib/marca.ts` — `SLUG = "arcada"`, `NOME = "Arcada"`, monograma `A`.
- `src/ui/index.ts` — barril sem a seção da casca.
- `src/App.tsx` — usa `GlassCard` e `StatCard`.

## 🧠 Decisões técnicas

- Kit copiado do Design-moderno depois do merge das duas correções: o Arcada não herda os defeitos que o Alivium teve de corrigir à mão.
- Barril sem a casca por enquanto: a casca depende do `react-router-dom`, que entra no PR dela.

## 🧪 Como testar

1. `npm run dev` → cartão de vidro e um `StatCard` "Consultas hoje".
2. `npm run lint && npm run type-check && npm test && npm run build`.

## 📎 Documentação afetada

- [[ADR-002-sistema-visual-vidro-organico]]
- [[linguagem-visual]]
- [[2026]] (changelog)
