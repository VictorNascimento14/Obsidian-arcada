---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 42
url: https://github.com/VictorNascimento14/Arcada/pull/42
branch: docs/exemplo-cro
tags: [pr, fundacao, docs]
status: aberto
---

# PR #42 — docs(claude): usar um CRO válido como exemplo estável

## 🎯 Contexto

Achado na revisão do PR #38 ([[2026-09-30-plano-da-v1]], item 5.6). Fecha a issue #40.

## 🔧 Mudanças

- `CLAUDE.md` — exemplo estável de profissional: `Dra. Exemplo` / `CRO-SP 00000`.

## 🧠 Decisões técnicas

- Exemplo com UF real e número zerado: passa na validação e não corresponde a um registro plausível.

## 🧪 Como testar

1. `croValido("CRO-SP 00000")` → `true`; `croValido("CRO-UF 00000")` → `false`.

## 📎 Documentação afetada

- [[2026]] (changelog)
