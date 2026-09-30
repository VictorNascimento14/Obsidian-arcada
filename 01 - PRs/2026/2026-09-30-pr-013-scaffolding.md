---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 13
url: https://github.com/VictorNascimento14/Arcada/pull/13
branch: chore/scaffolding
tags: [pr, fundacao, scaffolding]
status: aberto
---

# PR #13 — chore: scaffolding Vite + React + TypeScript + ESLint

## 🎯 Contexto

Primeiro passo do [[2026-09-30-plano-da-v1]] (módulo 0, fundação). Fecha a issue #1.

## 🔧 Mudanças

- `package.json` — React 19, Vite 8, TypeScript 5.8, ESLint 9; scripts `dev`, `build`, `preview`, `lint`, `type-check`.
- `vite.config.ts` — porta 3000, saída em `out/`, alias `@/` → `src/`, `BASE_PATH` para deploy em subpasta.
- `tsconfig*.json` — TypeScript estrito, com `noUnusedLocals` e `noUnusedParameters`.
- `eslint.config.js` — regras de hooks e de fast refresh.
- `index.html`, `src/main.tsx`, `src/App.tsx`, `src/vite-env.d.ts` — a página mínima.

## 🧠 Decisões técnicas

- A configuração vem do template do kit vidro-orgânico ([[ADR-002-sistema-visual-vidro-organico]]); Tailwind e kit entram nos PRs seguintes, para cada PR ter um assunto só.
- `react-router-dom` fica para o PR da casca, o primeiro que o usa.

## 🧪 Como testar

1. `npm install && npm run dev` → `localhost:3000` mostra "Arcada".
2. `npm run lint && npm run type-check && npm run build`.

## 📎 Documentação afetada

- [[runbook-rodar-local]]
- [[2026]] (changelog)
