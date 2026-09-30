---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, visao-do-dia]
---

# Agenda do dia

## O que é

A tela de entrada do módulo Agenda: o dia da clínica por cadeira. Cada cadeira é uma coluna, o horário corre
na vertical e cada consulta é um cartão do tamanho da duração. É por ela que a recepção vê quem vem, em que
cadeira, com quem e a que horas. O seletor **Dia** e **Semana** troca a visão: a semana é a [[AgendaDaSemana]], e o calendário do mês ([[CalendarioDoMes]]) leva a qualquer dia.

## Onde está no código

- `src/modulos/agenda/modulo.ts` — a rota `/agenda` e o item `Agenda` da coluna (grupo Consultório, ordem 10,
  ícone `calendar`, também na barra do celular).
- `src/modulos/agenda/PaginaAgenda.tsx` — a tela: o seletor Dia/Semana, a navegação, o título, o aviso de feriado, o calendário e os modais que ela abre (marcar ou remarcar, e o [[DetalheDaConsulta]]).
- `src/modulos/agenda/GradeDoDia.tsx` — a grade: lê as coleções `consultas`, `cadeiras`, `clinica`, `pacientes`,
  `profissionais` e `procedimentos` por `useColecao` e desenha as colunas.
- `src/modulos/agenda/CartaoDaConsulta.tsx` — o cartão da consulta, que é o botão que abre o detalhe.
- `src/modulos/agenda/DetalheDaConsulta.tsx` e `mudarSituacao.ts` — o modal do detalhe e a gravação da situação
  e do cancelamento.
- `src/modulos/agenda/GradeDaSemana.tsx` — a visão da semana ([[AgendaDaSemana]]).
- `src/modulos/agenda/grade.ts` — `montarGrade`: a janela de horas, as marcas de hora cheia e os trechos fechados.
- `src/modulos/agenda/dias.ts` — `somarDias` e `rotuloDoDia`.
- `src/modulos/agenda/sementes.ts` — as dez consultas de demonstração.
- Regras que a tela reaproveita: `feriados.ts` (`feriadoDoDia`), `horarios.ts` (`diaDaSemana`, `emMinutos`,
  `emHora`) e `situacao.ts` (`ROTULO_DA_SITUACAO`).

## Comportamento

- **Dia**: abre em hoje. `Dia anterior` e `Próximo dia` andam um dia; `Hoje` volta ao dia atual e fica
  desabilitado quando já está nele. O título traz o dia por extenso (`Quarta-feira, 30 de setembro de 2026`).
- **Colunas**: uma por cadeira ativa, na ordem do cadastro. Cadeira inativa só aparece no dia em que ainda tem
  consulta (marcada antes de inativar, não some). Com mais cadeiras do que cabe na tela, a grade rola de lado.
- **Eixo do horário**: 7 rem por hora. A janela cobre o expediente do dia em horas cheias e cresce para a consulta
  que cai fora dele; o que não é expediente (almoço, antes de abrir, depois de fechar) aparece numa faixa mais clara.
- **Cartão**: paciente, `início–fim` (e ` · procedimento`, quando a consulta tem um), situação e profissional.
  A borda esquerda e o tom de fundo são a cor do profissional. Paciente ou profissional removido depois da
  marcação aparece como `Paciente removido` / `Profissional removido`, e a consulta continua na grade.
- **Situações na grade**: a consulta cancelada libera o horário e não aparece; a que faltou continua ocupando e aparece.
- **Resumo**: `3 consultas neste dia`, `1 consulta neste dia` ou `Nenhuma consulta neste dia` — região
  `role="status"`. Dia fechado com consulta marcada acrescenta `— a clínica não atende neste dia`.
- **Avisos**: `Feriado: <nome>.` e `Ponto facultativo: <nome>.` acima da grade; `Clínica fechada neste dia`
  no lugar da grade quando o expediente não tem horário e não há consulta; `Nenhuma cadeira cadastrada` sem cadeira.
- **Sementes**: dez consultas de segunda a sexta da semana de hoje (no sábado e no domingo, da semana que vem),
  com as datas calculadas na hora de semear; o dia que já passou fica `concluida` e feriado é pulado. Só entram
  se a coleção `consultas` estiver vazia, e cada uma traz um procedimento do catálogo padrão ([[CatalogoPadrao]]).
- **Marcar consulta**: o botão **Marcar consulta** abre o modal de marcação ([[MarcarConsulta]]).
- **Detalhe da consulta**: o cartão inteiro é um botão que abre o [[DetalheDaConsulta]], onde se muda a
  situação, com um botão por passo válido. Dali também se remarca e se cancela a consulta que ainda aguarda o
  atendimento ([[RemarcarECancelarConsulta]]) e se abre o WhatsApp do paciente com a confirmação pronta
  ([[ConfirmacaoPeloWhatsApp]]).
- **Semana e mês**: o seletor **Dia** e **Semana** troca a grade do dia pela de sete colunas
  ([[AgendaDaSemana]]), e o calendário do mês leva a qualquer dia ([[CalendarioDoMes]]).

## Movimento e micro-interações

O cartão de vidro que envolve a grade sobe ao entrar na tela, e os botões de dia usam a pílula do `Calendar` do
kit, com o `press` no clique. O kit já respeita `prefers-reduced-motion`.

## Histórico de mudanças

- [[2026-09-30-pr-079-agenda-dia]] — a tela, a grade por cadeira, o cartão da consulta e as sementes.
- [[2026-09-30-pr-093-agenda-marcar]] — o botão **Marcar consulta**, e as sementes passam a trazer procedimento.
- [[2026-09-30-pr-102-agenda-calendario]] — o calendário do mês ([[CalendarioDoMes]]).
- [[2026-09-30-pr-118-agenda-situacao-tela]] — o cartão abre o detalhe da consulta, onde se muda a situação.
- [[2026-09-30-pr-133-agenda-remarcar]] — remarcar e cancelar a consulta, pelo detalhe.
- [[2026-09-30-pr-145-agenda-semana]] — a visão da semana e o seletor Dia/Semana.
- [[2026-09-30-pr-153-agenda-whatsapp]] — a confirmação pelo WhatsApp, pelo detalhe.
