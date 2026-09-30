---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, visao-da-semana]
---

# Agenda da semana

## O que é

A semana da agenda: sete colunas, de segunda a domingo, cada uma com as consultas do dia em ordem de horário. É a
segunda visão da tela da [[AgendaDoDia]]: o seletor **Dia** e **Semana** troca uma pela outra.

## Onde está no código

- `src/modulos/agenda/GradeDaSemana.tsx` — a semana. Lê as coleções `consultas`, `cadeiras`, `clinica`,
  `pacientes`, `profissionais` e `procedimentos`.
- `src/modulos/agenda/PaginaAgenda.tsx` — o seletor, a navegação por semana, o título e o lugar do calendário.
- `src/modulos/agenda/dias.ts` — `inicioDaSemana`, `diasDaSemana` e `rotuloDaSemana`.
- `src/modulos/agenda/CartaoDaConsulta.tsx` — o mesmo cartão do dia, com a cadeira numa linha a mais (`cadeira`).

## Comportamento

- **Seletor**: **Dia** e **Semana** (`aria-pressed`). Trocar mantém o dia: a semana é a que contém o dia que estava
  aberto, e voltar para **Dia** mostra esse mesmo dia.
- **Semana**: de segunda a domingo. O calendário do mês do kit começa no domingo; a agenda começa na segunda, como as
  sementes, e deixa o domingo, que a clínica costuma fechar, na ponta. O título traz o intervalo: `28 de setembro a
  4 de outubro de 2026`, `5 a 11 de outubro de 2026`; mês e ano só onde mudam.
- **Navegação**: as setas viram **Semana anterior** e **Próxima semana** e andam sete dias. **Hoje** volta à
  semana de hoje e fica desabilitado quando ela já é a mostrada.
- **Colunas**: o cabeçalho traz o dia da semana abreviado e o número (hoje, na pílula cheia); embaixo, os cartões em
  ordem de horário. A cancelada libera o horário e não aparece; a que faltou aparece. O cartão diz paciente, horário,
  procedimento, situação, profissional e cadeira, e abre o [[DetalheDaConsulta]].
- **Cabeçalho do dia**: é um botão que abre esse dia na visão do dia.
- **Dia sem consulta**: `Sem consultas`; dia sem expediente, `Fechado`. Feriado e ponto facultativo trazem `Feriado:
  <nome>` ou `Ponto facultativo: <nome>` sob o cabeçalho, e as consultas que já existirem nele aparecem.
- **Resumo**: `3 consultas nesta semana`, `1 consulta nesta semana` ou `Nenhuma consulta nesta semana`, em região
  `role="status"`.
- **Tela larga (`lg`, 1024 px ou mais)**: sete colunas lado a lado, de no mínimo 8 rem cada; se não couberem, a semana
  rola de lado. Abaixo disso os dias se empilham.
- **Calendário do mês**: na semana ele não fica ao lado da grade, porque as sete colunas precisam da largura toda. O
  botão **Mês** o abre acima, em qualquer largura, e clicar num dia dele mostra a semana desse dia.
- **Não faz**: desenhar as consultas na escala de horas do dia. Duas consultas no mesmo horário em cadeiras
  diferentes ficam uma abaixo da outra na coluna, e o cartão diz a cadeira.

## Movimento e micro-interações

O cabeçalho do dia usa o `press` do kit, escurece de leve no `hover` e mostra o foco como anel (`focus-visible`).

## Histórico de mudanças

- [[2026-09-30-pr-145-agenda-semana]] — a visão da semana.
