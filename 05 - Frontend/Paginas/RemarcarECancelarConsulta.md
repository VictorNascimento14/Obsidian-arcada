---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda (modal)
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, remarcar, cancelar, modal]
---

# Remarcar e cancelar consulta

## O que é

Os dois botões que o [[DetalheDaConsulta]] ganha para a consulta que ainda aguarda o atendimento (agendada ou
confirmada). **Remarcar** troca o detalhe pelo formulário do [[MarcarConsulta]], já preenchido com a consulta.
**Cancelar consulta** pede o motivo.

## Onde está no código

- `src/modulos/agenda/DetalheDaConsulta.tsx` — os dois botões e o campo do motivo; Remarcar chama `aoRemarcar`.
- `src/modulos/agenda/PaginaAgenda.tsx` — fecha o detalhe e abre o formulário com a consulta (`remarcar`).
- `src/modulos/agenda/MarcarConsulta.tsx` — a prop `remarcar`: o mesmo formulário, regravando aquela consulta.
- `src/modulos/agenda/marcar.ts` — `camposDaRemarcacao`, `remarcarConsulta` e o parâmetro `ignorar` de
  `restricoesDaAgenda` e `horariosSugeridos`.
- `src/modulos/agenda/mudarSituacao.ts` — `cancelarConsulta` e `MOTIVO_MAX`. `situacao.ts` — `aguardaAtendimento`.
- `src/dominio/consulta.ts` — o campo opcional `motivoCancelamento` da `Consulta`.

## Comportamento

- **Quem remarca e cancela**: só a consulta agendada ou confirmada (`aguardaAtendimento`). Em atendimento,
  concluída e faltou não têm os botões, e a função de gravar também recusa.
- **Remarcar**: o formulário abre com o título `Remarcar consulta` e traz paciente (travado: outro paciente é outra
  consulta), profissional, cadeira, procedimento, data, início e duração da consulta. Mudar qualquer um e clicar em
  **Remarcar** regrava a mesma consulta (o `id` não muda, nada é criado), avisa `Consulta remarcada` e abre a agenda
  no dia dela.
- **Conflito e feriado**: as mesmas regras e mensagens da marcação, ao vivo, e o botão fica desabilitado. A própria
  consulta não conta: o horário atual dela aparece entre os **Horários livres** e dá para remarcar por cima dele
  (11:00 numa consulta de 10:30 às 12:00, por exemplo).
- **Confirmada**: o formulário avisa `Esta consulta está confirmada. Ao remarcar, ela volta para agendada: o paciente
  precisa confirmar o novo horário.` e a consulta é gravada como agendada.
- **Profissional ou cadeira inativos**: o formulário só lista os ativos. Se a consulta estava com um que ficou
  inativo, o campo aparece vazio e a gravação pede um ativo (`Escolha um profissional ativo.`).
- **Cancelar**: o botão **Cancelar consulta** troca os botões do detalhe pelo campo **Motivo do cancelamento**
  (com o foco, até 200 caracteres). **Confirmar cancelamento** sem motivo mostra `Informe o motivo do
  cancelamento.` e não grava; com motivo grava a situação `cancelada` e o texto (sem os espaços das pontas) em
  `motivoCancelamento`, avisa `Consulta cancelada` e fecha. **Voltar** desiste.
- **A cancelada sai da grade** (libera o horário), e por isso o motivo, embora gravado, ainda não aparece em tela
  nenhuma: ele aparece onde as consultas canceladas forem listadas.

## Movimento e micro-interações

Nada novo. O campo do motivo recebe o foco ao abrir (`autoFocus`), porque o botão que o abriu sai do ar. O
formulário de remarcar é o `Modal` do kit, no lugar do detalhe: dois modais empilhados brigariam pelo Esc e pelo foco.

## Histórico de mudanças

- [[2026-09-30-pr-133-agenda-remarcar]] — remarcar e cancelar a consulta com motivo.
