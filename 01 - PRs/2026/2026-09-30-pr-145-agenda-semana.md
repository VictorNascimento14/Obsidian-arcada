---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 145
url: https://github.com/VictorNascimento14/Arcada/pull/145
branch: feat/agenda-semana
tags: [pr, agenda, semana]
status: aberto
---

# PR #145 — feat(agenda): mostrar a semana da agenda em sete colunas

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.4, a visão da semana ao lado da [[AgendaDoDia]]. Fecha a issue #137.

## 🔧 Mudanças

- `GradeDaSemana.tsx` (novo): as sete colunas, com as consultas do dia em ordem de horário, o resumo da semana e o aviso de dia fechado e de feriado.
- `dias.ts`: `inicioDaSemana`, `diasDaSemana` e `rotuloDaSemana` (a semana vai de segunda a domingo).
- `PaginaAgenda.tsx`: o seletor Dia | Semana, a navegação por semana, o título, o `Hoje` da semana e o calendário fora da coluna lateral na semana.
- `CartaoDaConsulta.tsx`: a prop `cadeira`, uma linha a mais no cartão. `CalendarioDoMes.tsx`: só o comentário (o dia aberto pode ser o dia ou a semana dele).
- Testes: 11 novos (3 em `dias.test.ts` e 8 em `PaginaAgenda.test.tsx`).

## 🧠 Decisões técnicas

- A semana começa na segunda: o domingo, que a clínica costuma fechar, fica na ponta direita e não no primeiro lugar. O calendário do mês do kit começa no domingo, e o kit não se mexe.
- Sem escala de horas: a coluna é uma fila de cartões em ordem de horário. Numa escala de horas, consultas simultâneas em cadeiras diferentes ficariam uma sobre a outra; a fila evita isso e o cartão diz a cadeira.
- O cabeçalho do dia é um botão que abre o dia: sem ele, ir da semana a um dia pedia o calendário e o seletor.
- Clicar num dia do calendário mostra esse dia na visão aberta (a semana dele, se for a semana), como o mini-calendário de outras agendas; quem troca de visão é o cabeçalho do dia.
- Na semana o calendário do mês sai da coluna lateral: ao lado dele, a semana tinha 784 px numa tela de 1440 px, as sete colunas não cabiam nos 8 rem mínimos e o domingo ficava fora da vista até rolar. O botão Mês o abre acima.
- Abaixo de `lg` os dias se empilham; a partir dele são sete colunas de no mínimo 8 rem, e a semana rola de lado se não couber.
- O estado `semana` mora na página: as duas grades recebem só o dia e os callbacks, e o detalhe da consulta continua sendo um só.

## ⚠️ Armadilhas e aprendizados

- A lista de cartões da coluna é um `grid`. Sem `grid-cols-1`, a trilha automática cresce até o texto sem quebra do cartão (o `truncate` não conta para o mínimo) e o cartão vaza da coluna, por baixo da vizinha. Só apareceu conferindo no navegador.
- `Hoje` fica desabilitado quando a semana mostrada já contém hoje, e não só quando o dia é hoje: senão o botão mudava só o dia âncora e a tela parecia não reagir.
- O nome acessível do cabeçalho do dia é o dia por extenso (`sr-only`); a abreviação e o número são `aria-hidden`.
- Cadeira removida aparece como `Cadeira removida` na semana; na grade do dia, a consulta dessa cadeira nem aparece (só há coluna de cadeira cadastrada).

## 🧪 Como testar

1. Rode `npm run dev`, abra `/agenda` e clique em Semana: sete colunas, de segunda a domingo, com o dia de hoje em destaque e o título com o intervalo.
2. Confira que cada dia lista as consultas em ordem de horário, com a cadeira, e que a cancelada não aparece. Sábado sem consulta diz `Sem consultas` e domingo, `Fechado`.
3. Use as setas: Próxima semana e Semana anterior andam sete dias, e Hoje volta à semana de hoje.
4. Clique num cartão: abre o detalhe da consulta. Clique no cabeçalho de um dia: abre esse dia na visão do dia.
5. Estreite a janela para menos de 1024 px: os dias se empilham. Clique em Mês: o calendário abre acima da semana.
6. Rode `npx vitest run --maxWorkers=2 src/modulos/agenda`.

## 📎 Documentação afetada

- [[AgendaDaSemana]]
- [[AgendaDoDia]]
- [[DetalheDaConsulta]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
