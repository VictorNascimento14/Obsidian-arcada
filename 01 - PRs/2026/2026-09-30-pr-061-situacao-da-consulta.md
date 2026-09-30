---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 61
url: https://github.com/VictorNascimento14/Arcada/pull/61
branch: feat/agenda-situacao
tags: [pr, agenda, situacao]
status: merged
---

# PR #61 — feat(agenda): definir as transições válidas da situação da consulta

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.6 — só a regra; os botões de situação vêm com a tela da agenda. Fecha a issue #47.

## 🔧 Mudanças

- `src/modulos/agenda/situacao.ts` — `transicoesDe` e `podeTransitar`, sobre uma tabela `Record<SituacaoConsulta, …>`.
- `src/modulos/agenda/situacao.test.ts` — 7 testes.

## 🧠 Decisões técnicas

- Tabela `Record<SituacaoConsulta, readonly SituacaoConsulta[]>`: se uma situação nova entrar no tipo, o TypeScript reprova até ela ganhar a linha, e a regra não fica esquecida numa cadeia de `if`.
- A ordem da lista é a dos botões: o passo seguinte do atendimento primeiro (confirmar, iniciar, concluir), depois faltou e cancelada. A tela só percorre a lista.
- Sem atalho: a consulta agendada passa por `confirmada` antes de `em-atendimento`, como o item define. Se o dia a dia pedir o atalho, é uma linha na tabela e uma no teste (comentado no código).
- Depois de `em-atendimento` só existe `concluida`: o paciente já está na cadeira, então faltou e cancelada não se aplicam, e o item também não os lista.
- `podeTransitar` existe ao lado de `transicoesDe` porque quem grava a mudança em `src/dados/` precisa da pergunta sim ou não, e a tela precisa da lista; as duas saem da mesma tabela.

## ⚠️ Armadilhas e aprendizados

- As três situações finais têm lista vazia, e a lista vazia é o que a tela usa para não mostrar botão nenhum: não trate "sem transição" como erro.
- O teste confere as 36 combinações de situação e compara com a lista literal das sete válidas: uma transição a mais ou a menos na tabela, inclusive repetir a situação, reprova.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/agenda/situacao` → 7 testes: a lista de destinos de cada uma das seis situações, na ordem dos botões, e a conferência das 36 combinações, das quais só as sete transições da regra são aceitas.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
