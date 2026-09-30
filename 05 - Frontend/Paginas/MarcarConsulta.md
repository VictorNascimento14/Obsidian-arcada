---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda (modal)
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, marcar-consulta, modal]
---

# Marcar consulta

## O que é

O modal que a [[AgendaDoDia]] abre para marcar uma consulta: paciente, profissional, cadeira, procedimento,
data, início e duração. Barra o que não pode acontecer (duas consultas disputando a cadeira ou o profissional,
marcação em feriado nacional) e sugere os horários livres do dia. O mesmo formulário remarca uma consulta que já existe (prop `remarcar`): ver [[RemarcarECancelarConsulta]].

## Onde está no código

- `src/modulos/agenda/PaginaAgenda.tsx` — o botão **Marcar consulta** e o modal aberto por ele; ao marcar, a agenda abre no dia da consulta. Guarda se o formulário abre vazio ou com `remarcar` (aberto pelo **Remarcar** do detalhe).
- `src/modulos/agenda/MarcarConsulta.tsx` — o modal, montado só enquanto aberto (cada abertura começa do zero); com a prop `remarcar` (uma `Consulta`), abre preenchido com ela e regrava a mesma consulta.
- `src/modulos/agenda/marcar.ts` — a regra: `validarMarcacao`, `restricoesDaAgenda`, `horariosSugeridos`,
  `marcarConsulta` e as frases dos bloqueios; para a remarcação, `camposDaRemarcacao`, `remarcarConsulta` e o parâmetro `ignorar` de `restricoesDaAgenda` e `horariosSugeridos`.
- Reaproveita `conflitos.ts` (`conflitosDaConsulta`), `horarios.ts` (`horariosLivres`) e `feriados.ts`
  (`feriadoDoDia`). Lê as coleções `pacientes`, `profissionais`, `cadeiras`, `procedimentos`, `clinica` e
  `consultas`, e grava em `consultas`.

## Comportamento

- **Campos**: Paciente, Profissional, Cadeira, Procedimento (opcional), Data, Início e Duração (min). A data abre
  no dia que a agenda mostra e a duração em 30 min.
- **Só ativos**: profissional (`profissionalAtivo`), cadeira (`cadeiraAtiva`) e procedimento (`ativo`) inativos
  não aparecem nas listas. Sem procedimento cadastrado, o campo fica desabilitado (`Nenhum procedimento cadastrado`).
- **Procedimento e duração**: escolher o procedimento preenche a duração prevista dele (`duracaoMin`); dá para
  ajustar depois. A duração é um inteiro de 5 a 480 minutos, e a consulta não pode passar da meia-noite.
- **Horários livres**: botões com os inícios livres do dia (de 15 em 15 minutos, dentro do expediente) para a
  cadeira e o profissional escolhidos; com só um dos dois, só ele conta. Clicar num preenche o **Início**. Sem
  cadeira nem profissional, o modal pede um deles; sem vaga, diz `Nenhum horário livre neste dia para essa duração.`
- **Bloqueios** (mensagem em vermelho, botão **Marcar** desabilitado): `Cadeira 1 já tem consulta das 08:00 às
  08:45 (Ana Beatriz Moura).`; o mesmo para o profissional, ou `Cadeira 1 e Dra. Exemplo já têm…` quando os
  dois colidem. Encostar (uma termina quando a outra começa) não é conflito; a cancelada libera o horário e a
  que faltou continua ocupando. Feriado nacional: `<nome> é feriado nacional. A agenda não marca consulta nesse dia…`
- **Aviso**: ponto facultativo (Carnaval, Corpus Christi) só avisa — `<nome> é ponto facultativo. Confirme se a
  clínica abre…` — e a marcação segue possível.
- **Envio**: a consulta é gravada como `agendada`. Com campo faltando, o modal continua aberto e mostra o que
  falta em cada campo. Dá certo: avisa `Consulta marcada`, fecha e a agenda abre no dia da consulta. `marcarConsulta`
  confere de novo campos, ativos e agenda na hora de gravar.
- **Fora do que confere**: o horário dentro do expediente (a clínica encaixa) e data no passado.
- **Remarcar**: com a prop `remarcar`, o modal se chama `Remarcar consulta`, abre com os dados da consulta (o
  paciente fica travado), ignora a própria consulta nos conflitos e nos horários livres e regrava a mesma
  consulta; a que estava confirmada volta a agendada. O detalhe está em [[RemarcarECancelarConsulta]].

## Movimento e micro-interações

É o `Modal` do kit: cortina escurecida, painel de vidro que sobe ao abrir, foco no botão de fechar e devolvido a
quem abriu; fecha no Escape e no clique fora. Os horários livres são pílulas com `press` no clique; a marcada
fica no verde profundo (`aria-pressed`).

## Histórico de mudanças

- [[2026-09-30-pr-093-agenda-marcar]] — o modal, a regra de conflito e feriado e os horários livres.
- [[2026-09-30-pr-133-agenda-remarcar]] — a prop `remarcar`: o mesmo formulário regrava a consulta.
