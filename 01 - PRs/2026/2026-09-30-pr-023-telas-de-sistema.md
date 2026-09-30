---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 23
url: https://github.com/VictorNascimento14/Arcada/pull/23
branch: feat/telas-de-sistema
tags: [pr, fundacao, sistema]
status: aberto
---

# PR #23 — feat(sistema): telas de página não encontrada e erro inesperado

## 🎯 Contexto

Módulo 0 do [[2026-09-30-plano-da-v1]]. Fecha a issue #12.

## 🔧 Mudanças

- `src/sistema/Recado.tsx` — tela cheia de recado (vinda do Alivium, mesmo kit).
- `src/sistema/NaoEncontrada.tsx`, `src/sistema/ErroInesperado.tsx`.
- `src/rotas.tsx` — a árvore de rotas: raiz com `errorElement`, `RailLayout` com as rotas dos módulos, e `*` para a página não encontrada.
- `src/App.tsx` — só cria o roteador com `basename`.
- `src/rotas.test.tsx` — 404 e erro com `createMemoryRouter`.

## 🧠 Decisões técnicas

- Telas de sistema fora da coluna: o erro pode ser justamente da casca.
- Rotas separadas do roteador: o teste monta a mesma árvore em memória, em qualquer endereço, sem mexer em `window.location`.
- O detalhe do erro vai para o console, não para a tela.

## 🧪 Como testar

1. `npm run dev` → `localhost:3000/qualquer-coisa` mostra a página não encontrada.
2. `npm test` → 2 testes de rotas (404 com link para `/`; componente que lança erro cai na tela de erro).

## 📎 Documentação afetada

- [[arcada-frontend]]
- [[2026]] (changelog)
