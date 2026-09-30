---
tipo: funcionalidade
camada: frontend
area: Atendimento
rota: /atendimento/:consultaId
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, atendimento, consulta, agenda]
---

# Atendimento da consulta

## O que é

A entrada do atendimento: da consulta marcada na agenda o paciente passa a ser atendido. São duas partes: a aba
`Atendimentos` da [[FichaDoPaciente]], que lista as consultas do paciente de hoje em diante e oferece
`Iniciar atendimento`, e a tela `/atendimento/:consultaId`, que mostra a consulta que está sendo atendida. Iniciar leva a
consulta para `Em atendimento` pela mesma regra de situação da agenda que o [[DetalheDaConsulta]] usa.

## Onde está no código

- `src/modulos/atendimento/modulo.ts` — a rota `/atendimento/:consultaId` e a aba `Atendimentos` da ficha
  (`abaPaciente`, ordem 60). Sem item na coluna lateral: o atendimento começa pela consulta.
- `AbaAtendimentos.tsx` — a aba da ficha.
- `TelaDoAtendimento.tsx` — a tela da consulta.
- `BotaoIniciarAtendimento.tsx` — o botão que muda a situação e abre a tela.
- `consultas.ts` — `consultasAPartirDe` (regra pura: do paciente, de hoje em diante, por horário) e `faixaDeHoras`.
- Da agenda entram só `mudarSituacao`, `podeTransitar` e os rótulos de situação, importados: o módulo da agenda não
  é editado.

## Comportamento

### Aba `Atendimentos` da ficha

- Lista as consultas do paciente **de hoje em diante**, por ordem de horário; o dia de hoje entra mesmo que a hora
  já tenha passado. Cada linha traz o dia por extenso, a faixa de horas, o procedimento previsto (ou `Sem
  procedimento definido`), o profissional e a situação. A cancelada também aparece, com a situação escrita.
- **`Iniciar atendimento`** só nas consultas `Agendada` e `Confirmada`, as duas situações de onde a agenda permite ir
  para `Em atendimento`. Clicar grava a situação e abre `/atendimento/:consultaId`; se a agenda recusar, o motivo sai
  num aviso e nada muda.
- Na consulta `Em atendimento` o botão vira o link `Abrir atendimento`, que volta à tela dela.
- `Concluída`, `Faltou` e `Cancelada` mostram só a situação.

### Tela `/atendimento/:consultaId`

- Mostra o paciente (com o link da ficha), o dia e o horário, o profissional, o procedimento previsto e a situação.
- **Abrir o endereço não muda a situação.** Só o botão `Iniciar atendimento` muda: recarregar a página ou colar o
  endereço nunca inicia um atendimento sozinho. Consulta agendada ou confirmada mostra o botão e o aviso `O
  atendimento ainda não começou.`
- Consulta que não existe: `Atendimento não encontrado`, com o caminho de volta à agenda.

## Histórico de mudanças

- [[2026-09-30-pr-136-atendimento-iniciar]] — a aba `Atendimentos`, a rota da consulta e o iniciar atendimento.
