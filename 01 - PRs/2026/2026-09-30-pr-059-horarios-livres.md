---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 59
url: https://github.com/VictorNascimento14/Arcada/pull/59
branch: feat/agenda-horarios-livres
tags: [pr, agenda, horarios]
status: aberto
---

# PR #59 — feat(agenda): listar os horários livres do expediente

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.2 — só a parte pura; a sugestão na tela vem com "Marcar consulta" (8.5). Fecha a issue #46.

## 🔧 Mudanças

- `src/modulos/agenda/horarios.ts` — `horariosLivres`.
- `src/modulos/agenda/horarios.test.ts` — 16 testes.

## 🧠 Decisões técnicas

- Recebe o `Expediente` inteiro e a data, e escolhe a faixa pelo dia da semana: a tela não repete a conta do `getDay`. O "intervalo" do item é o vão entre duas faixas do `Expediente`, como o próprio tipo já descreve.
- A grade conta os passos de 15 minutos a partir da abertura de cada faixa, não do relógio: uma faixa que abre às 08:10 sugere 08:10, 08:25 e assim por diante. Com faixas em hora cheia dá no mesmo.
- A consulta tem de caber inteira numa faixa. Terminar junto com o fechamento vale (encostar não é sobrepor, como no conflito de horário, item 8.1); atravessar o almoço, não.
- `consultas` são as que ocupam quem vai atender: quem chama filtra pela cadeira e pelo profissional escolhidos, e ao remarcar tira a própria consulta da lista. Só `cancelada` não ocupa — a mesma regra do item 8.1.
- Feriado fica de fora: bloquear o dia é de `feriadoDoDia`, e o ponto facultativo é decisão da clínica. A tela compõe as duas regras.
- Dia mal formado (inclusive o campo de data vazio) e duração que não é positiva devolvem `[]`, sem lançar erro: o campo de data passa por vazio enquanto a pessoa digita.
- A consulta que passa da meia-noite não ocupa o dia seguinte (teto marcado com `ponytail:` no código): o expediente não cruza a meia-noite.

## ⚠️ Armadilhas e aprendizados

- O dia da semana sai de `Date.UTC` e `getUTCDay()`. `new Date("AAAA-MM-DD")` lê UTC e o `getDay()` local recuaria um dia no Brasil, e a agenda abriria com o expediente do dia errado; o teste dos sete dias roda em três fusos.
- O `Expediente` não promete faixas em ordem, então o resultado é ordenado no fim: `HH:mm` com zero à esquerda ordena como o relógio.

## 🧪 Como testar

1. `npm test -- src/modulos/agenda/horarios` → 16 testes: passo de 15 minutos até a consulta terminar junto com a faixa, passos contados da abertura de cada faixa, vão entre duas faixas, faixas fora de ordem, o expediente de cada dia da semana (domingo a sábado), dia fechado, consulta marcada (encostar não tira), cada situação da consulta, consultas de outros dias, consulta mais longa que a faixa e duração ou dia inválidos.
2. `TZ=Pacific/Kiritimati npm test -- src/modulos/agenda/horarios` (e `America/Sao_Paulo`, `Pacific/Pago_Pago`) → o mesmo resultado: o dia da semana não depende do fuso.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
