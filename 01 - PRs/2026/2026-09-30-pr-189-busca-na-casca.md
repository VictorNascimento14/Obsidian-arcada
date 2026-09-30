---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 189
url: https://github.com/VictorNascimento14/Arcada/pull/189
branch: feat/busca-na-casca
tags: [pr, sistema]
status: merged
---

# PR #189 — feat(sistema): abrir a busca global com Ctrl+K em qualquer tela

## 🎯 Contexto

Pendência do item 14.2 do [[2026-09-30-plano-da-v1]], registrada na nota [[BuscaGlobal]]. Fecha a issue #188.

## 🔧 Mudanças

- `src/rotas.tsx` — a raiz ganha `element` com `<BuscaGlobal />` e `<Outlet />`.
- `src/rotas.test.tsx` — Ctrl+K fora de `/sistema` abre o diálogo.

## 🧠 Decisões técnicas

- Na raiz, não no `App`: ao lado do `ToastHost` ficaria fora do roteador e `useNavigate` quebraria.
- A instância de `/sistema` continua lá: a guarda do #186 faz só uma responder.

## 🧪 Como testar

1. Em `/pacientes` ou `/agenda`, Ctrl+K (⌘K no Mac) abre a busca; Enter num resultado abre a ficha.
2. Em `/sistema` continua abrindo uma caixa só (guarda de instância única do #186).
3. `npx vitest run src/rotas.test.tsx`.

## 📎 Documentação afetada

- [[BuscaGlobal]]
- [[2026]] (changelog)
