---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 16
url: https://github.com/VictorNascimento14/Arcada/pull/16
branch: ui/fundacao-visual
tags: [pr, fundacao, design]
status: aberto
---

# PR #16 — ui(fundacao): instalar a fundação do sistema vidro-orgânico

## 🎯 Contexto

Módulo 0 do [[2026-09-30-plano-da-v1]]. Decisão em [[ADR-002-sistema-visual-vidro-organico]]. Fecha a issue #4.

## 🔧 Mudanças

- `src/ui/index.css` — cópia do kit, sem alteração.
- `tailwind.config.ts`, `postcss.config.ts` — cópia do kit.
- `index.html` — script que aplica `.dark` antes da primeira pintura (chave `arcada-tema`) e o CDN do Remix Icon.
- `src/main.tsx` importa o CSS; `src/App.tsx` usa `.glass`.
- `package.json` — `tailwindcss`, `postcss`, `autoprefixer`, `jiti`.

## 🧠 Decisões técnicas

- Fundação copiada sem alteração: mudar uma rampa para acertar uma tela repinta o app inteiro (invariante do kit).
- `jiti` porque os arquivos de configuração do Tailwind e do PostCSS são TypeScript.

## 🧪 Como testar

1. `npm run dev` → cartão de vidro sobre o fundo verde-claro.
2. Com o sistema em tema escuro, a página já abre escura (sem piscar claro).
3. `npm run build` → o CSS gerado tem `.glass`, `.dark` e as rampas em `oklch(`.

## 📎 Documentação afetada

- [[linguagem-visual]]
- [[ADR-002-sistema-visual-vidro-organico]]
- [[2026]] (changelog)
