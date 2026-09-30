---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 52
url: https://github.com/VictorNascimento14/Arcada/pull/52
branch: feat/agenda-conflitos
tags: [pr, agenda, conflitos]
status: merged
---

# PR #52 — feat(agenda): detectar conflito de horário por cadeira e profissional

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.1 — só a parte pura; o bloqueio na tela vem com "Marcar consulta" (8.5). Fecha a issue #45.

## 🔧 Mudanças

- `src/modulos/agenda/conflitos.ts` — `conflitosDaConsulta` e os tipos `Reserva` (a candidata: `cadeiraId`, `profissionalId`, `inicio`, `duracaoMin` e `id` opcional), `Conflito` e `Motivo`.
- `src/modulos/agenda/conflitos.test.ts` — 19 testes.

## 🧠 Decisões técnicas

- Intervalo semiaberto `[início, início + duração)`: a sobreposição é `inicioA < fimB && inicioB < fimA`, então a consulta que termina às 10:00 e a que começa às 10:00 não conflitam.
- Conflito é cadeira **ou** profissional. O resultado traz `motivos` (o que colidiu) para a tela escrever a mensagem certa sem repetir a comparação.
- Só `cancelada` libera o horário. `faltou` continua ocupando, como o item pede ("não canceladas"); mudar isso é uma condição no `if`.
- A candidata é uma `Reserva` (quatro campos e `id` opcional), não uma `Consulta`: quem marca ainda não tem `id` nem situação. Com `id`, a consulta ignora a si mesma na lista — é o que a remarcação precisa, sem a tela filtrar antes.
- Minutos por `Date.UTC`, sem `toISOString()` e sem `Date` local: em UTC todo dia tem 24 h, então somar a duração não erra na virada do dia e o resultado não muda com o fuso. Conferido rodando os testes em três fusos.
- Não confere o formato de `inicio` (o teto está marcado com `ponytail:` no código): quem grava em `src/dados/` valida e a tela só chama com data e hora escolhidas. Texto solto exigiria regex e `RangeError`, como em `idade`.

## ⚠️ Armadilhas e aprendizados

- `<=` no lugar de `<` faria da consulta que começa quando a outra termina um conflito: os dois lados de "encostar" estão no teste.
- Ao remarcar, a consulta ainda está na lista com o horário antigo: sem o `id`, remarcar de 09:00–10:00 para 09:30–10:30 conflitaria com o próprio horário de antes.

## 🧪 Como testar

1. `npm test -- src/modulos/agenda/conflitos` → 19 testes: o motivo (cadeira, profissional ou os dois), a sobreposição (começa antes, termina depois, cabe dentro, cobre, idêntica), encostar dos dois lados sem conflito, outro dia, cada situação da consulta, a remarcação e a lista com mais de um conflito.
2. `TZ=Pacific/Kiritimati npm test -- src/modulos/agenda/conflitos` (e `America/Sao_Paulo`, `Pacific/Pago_Pago`) → o mesmo resultado: nada depende do fuso.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
