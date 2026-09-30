---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda (modal)
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, situacao, modal]
---

# Detalhe da consulta

## O que é

O modal que a [[AgendaDoDia]] abre ao clicar no cartão de uma consulta: mostra quem, quando, onde e a situação, e
oferece um botão para cada passo que a consulta pode dar. É por onde a situação muda; remarcar, cancelar e a
confirmação pelo WhatsApp entram aqui nos itens seguintes da agenda.

## Onde está no código

- `src/modulos/agenda/DetalheDaConsulta.tsx` — o modal. Recebe `consultaId` e lê a consulta, o paciente, o
  profissional, a cadeira e o procedimento das coleções: o que mostra e oferece é sempre o gravado.
- `src/modulos/agenda/mudarSituacao.ts` — a gravação: confere que a consulta existe e que `podeTransitar`, e salva.
- `src/modulos/agenda/situacao.ts` — a regra: `transicoesDe`, `podeTransitar`, `ROTULO_DA_SITUACAO` (o nome da
  situação) e `ACAO_DA_SITUACAO` (o verbo do botão).
- `src/modulos/agenda/CartaoDaConsulta.tsx` — o cartão é o botão que abre o detalhe; `GradeDoDia.tsx` repassa o
  clique (`aoAbrirConsulta`) e `PaginaAgenda.tsx` guarda o id da consulta aberta.

## Comportamento

- **Abrir**: o cartão inteiro é o botão (`aria-haspopup="dialog"`). O modal se chama `Consulta de <paciente>`; o
  X, o Esc e o clique fora o fecham.
- **Campos**: Quando (o dia por extenso, das HH:mm às HH:mm), Profissional, Cadeira, Procedimento (`Sem
  procedimento definido`) e Situação. Cadastro removido aparece como `Paciente removido`, `Profissional removido`
  ou `Cadeira removida`.
- **Botões** (grupo `Mudar a situação`): um por transição válida, na ordem de `transicoesDe`, o primeiro em
  destaque. Agendada: Confirmar consulta, Iniciar atendimento, Marcar falta. Confirmada: Iniciar atendimento,
  Marcar falta. Em atendimento: Concluir atendimento. Concluída e Faltou não têm botão e dizem `Situação final:
  esta consulta não muda mais.`
- **Ao clicar**: grava, avisa `Situação atualizada` (`<paciente> · <situação>`) e fecha; o foco volta ao cartão,
  que já mostra a situação nova. Se a gravação for recusada (a consulta mudou por baixo), o aviso diz `Não foi
  possível mudar a situação` com o motivo e o modal continua aberto.
- **Não faz (ainda)**: cancelar (pede motivo), remarcar e confirmar por WhatsApp. Também não confere a data: dá
  para registrar a falta de uma consulta futura.
- **Consulta em cadeira removida** não aparece na grade do dia (a grade só desenha colunas de cadeira
  cadastrada), então não abre por ali.

## Movimento e micro-interações

O modal é o `Modal` do kit: cortina, entrada `animate-rise`, foco no botão de fechar ao abrir e de volta ao cartão
ao fechar. O cartão escurece de leve no `hover` e ganha um anel de foco por dentro (`focus-visible`, `ring-inset`:
o cartão corta o que passa da borda).

## Histórico de mudanças

- [[2026-09-30-pr-118-agenda-situacao-tela]] — o detalhe da consulta e os botões das transições de situação.
