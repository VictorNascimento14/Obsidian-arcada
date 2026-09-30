---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, calendario]
---

# Calendário do mês

## O que é

O calendário do mês da [[AgendaDoDia]]: mostra de relance quais dias têm consulta e leva a qualquer um deles
com um toque. É o `Calendar` do kit, sem código de calendário próprio.

## Onde está no código

- `src/modulos/agenda/CalendarioDoMes.tsx` — o cartão com o `Calendar` do kit; recebe `aoAbrirDia`.
- `src/modulos/agenda/dias.ts` — `diasComConsulta`: os dias que vão em `marcados`.
- `src/modulos/agenda/PaginaAgenda.tsx` — onde ele fica na tela (ao lado da grade ou atrás do botão **Mês**) e
  o que acontece ao clicar num dia.
- Lê a coleção `consultas` por `useColecao`; a marca acompanha as consultas marcadas na hora (ver [[MarcarConsulta]]).

## Comportamento

- **Marca**: o ponto embaixo do número, nos dias que têm consulta na grade. A cancelada não marca; a que faltou marca.
  O dia de hoje aparece na pílula verde cheia do kit e, nele, a marca não se vê.
- **Clicar num dia** abre esse dia: o título e a grade passam a ser dele, e o botão **Hoje** volta a valer.
- **O mês é do calendário**: as setas dele andam de mês sem mudar o dia aberto, e ele abre sempre no mês de hoje.
  Ir ao dia anterior ou ao seguinte, ou marcar uma consulta em outro mês, não muda o mês que o calendário mostra.
- **Tela larga (`xl`, 1280 px ou mais)**: o calendário fica numa coluna de 19 rem ao lado da grade e acompanha a
  rolagem do dia (`sticky`).
- **Celular e tablet**: o calendário fica escondido; o botão **Mês** (`aria-expanded`) o abre e fecha, e escolher
  um dia o fecha. Nessa largura o título do dia ocupa a linha de cima e o botão **Marcar consulta** mostra só o `+`
  (o nome continua no leitor de tela).
- **Não faz**: marcar feriado no calendário, nem destacar o dia aberto (o kit só tem a pílula de hoje).

## Movimento e micro-interações

O cartão de vidro sobe ao entrar na tela. Os dias e as setas usam o `press` do kit, e o calendário respeita
`prefers-reduced-motion` como o resto do sistema.

## Histórico de mudanças

- [[2026-09-30-pr-102-agenda-calendario]] — o calendário do mês na agenda, com a marca dos dias que têm consulta.
