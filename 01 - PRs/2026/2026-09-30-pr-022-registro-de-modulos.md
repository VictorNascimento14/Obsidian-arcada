---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 22
url: https://github.com/VictorNascimento14/Arcada/pull/22
branch: feat/registro-de-modulos
tags: [pr, fundacao, modulos]
status: merged
---

# PR #22 — feat(modulos): registrar módulos por pasta com import.meta.glob

## 🎯 Contexto

Módulo 0 do [[2026-09-30-plano-da-v1]]. Implementa [[ADR-003-modulos-por-pasta-com-registro-automatico]]. Fecha a issue #10.

## 🔧 Mudanças

- `src/modulos/tipos.ts` — `Modulo` e os grupos da coluna (Consultório, Gestão, Cadastros).
- `src/modulos/navegacao.ts` (+ teste) — `montarNavegacao`: ordena por `ordem` com desempate pela `chave`, agrupa, junta rotas, barra do celular e abas do paciente.
- `src/modulos/index.ts` — `import.meta.glob("./*/modulo.ts", { eager: true })`.
- `src/modulos/painel/` — o Painel movido de `src/paginas/`.
- `src/App.tsx` — rotas e coluna do registro; `src/navegacao.tsx` fica só com a conta.

## 🧠 Decisões técnicas

- `modulo.ts` sem JSX, com `Component` na rota: o arquivo exporta só um objeto, sem briga com a regra de fast refresh.
- `eager: true`: o roteador precisa de todas as rotas quando é criado.
- Grupos da coluna numa lista fixa em `tipos.ts`: a ordem dos grupos é decisão de produto, não de módulo.
- Barra do celular vazia cai no padrão do kit (todos os itens), em vez de uma barra sem nada.

## ⚠️ Armadilhas e aprendizados

- Desempate pela `chave` na ordem: sem ele, dois módulos com a mesma `ordem` trocariam de lugar conforme a ordem em que o `glob` devolve os arquivos.

## 🧪 Como testar

1. `npm test` → 4 testes de `montarNavegacao` (ordem, grupos, rotas, barra e abas) + o do app.
2. `npm run dev` → a coluna mostra o Painel, vindo do registro.

## 📎 Documentação afetada

- [[ADR-003-modulos-por-pasta-com-registro-automatico]]
- [[arcada-frontend]]
- [[2026]] (changelog)
