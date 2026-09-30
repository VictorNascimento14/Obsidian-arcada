---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 173
url: https://github.com/VictorNascimento14/Arcada/pull/173
branch: fix/ficha-aba-ativa
tags: [pr, pacientes]
status: aberto
---

# PR #173 — fix(pacientes): rolar a barra de abas da ficha até a aba ativa

## 🎯 Contexto

Defeito visto na trilha do periodontograma ([[2026-09-30-plano-da-v1]], módulo 4), no componente da ficha ([[FichaDoPaciente]]). Fecha a issue #172.

## 🔧 Mudanças

- `src/modulos/pacientes/FichaPaciente.tsx` — efeito na troca de aba: `scrollIntoView({ block: "nearest", inline: "nearest" })` na aba ativa, `smooth` ou `auto` conforme `usePrefersReducedMotion`.
- `src/modulos/pacientes/FichaPaciente.rolagem.test.tsx` — a aba clicada é a que rola, sem sair da linha.

## 🧠 Decisões técnicas

- `block: "nearest"`: rolar só o eixo da barra, sem pular a página na vertical.
- Chamada opcional (`?.`) do método: o jsdom não implementa `scrollIntoView`; o teste põe um dublê e o tira.

## 🧪 Como testar

1. Celular (390 px): abrir uma ficha e ir até a última aba pelas setas ou tocando na ponta — a aba aparece inteira.
2. `npx vitest run src/modulos/pacientes/FichaPaciente.rolagem.test.tsx`.

## 📎 Documentação afetada

- [[FichaDoPaciente]]
- [[2026]] (changelog)
