---
tipo: funcionalidade
camada: frontend
area: Painel
rota: /
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, painel, agenda, consulta]
---

# Consultas de hoje

## O que é

O bloco do [[PainelDoConsultorio]] que mostra o dia da agenda: as consultas de hoje em ordem de horário, cada uma com
a situação, e a próxima em destaque. Responde a "quem vem agora?" sem abrir a agenda; o link `Abrir a agenda` leva a
ela ([[AgendaDoDia]]).

## Onde está no código

- `src/modulos/painel/ConsultasDeHoje.tsx` — o bloco (cartão de vidro; região com o nome `Consultas de hoje`).
- `src/modulos/painel/hoje.ts` — `agoraISO`, `consultasDeHoje` e `proximaConsulta`, a regra pura; o teste está em
  `hoje.test.ts`.
- Lê `consultas`, `pacientes` e `profissionais` de `src/dados/colecoes.ts` e reaproveita `ROTULO_DA_SITUACAO` e
  `aguardaAtendimento` da agenda.

## Comportamento

- **O que entra**: as consultas do dia de hoje (o dia sai de `diaISO`). A cancelada libera o horário e não conta, como
  na grade da agenda; concluída e faltou entram, com a situação.
- **A ordem** é a do horário; duas no mesmo horário ficam na ordem em que foram marcadas.
- **Cada linha**: o horário, o nome do paciente, o profissional e a pílula da situação (`Agendada`, `Confirmada`,
  `Em atendimento`, `Concluída`, `Faltou`). Paciente ou profissional removido depois da marcação aparece como
  `Paciente removido` ou `Profissional removido`, em vez de a consulta sumir.
- **A próxima consulta** fica em destaque acima da lista, com a hora grande: é a primeira que ainda aguarda o
  atendimento (agendada ou confirmada) e não começa antes de agora. Em atendimento, concluída e faltou não são
  "próximas", nem a que passou da hora sem começar; essa segue na lista com a situação que tem.
- **Sem próxima**, com consultas no dia: `Nenhuma consulta por começar hoje.` no lugar do destaque, e a lista segue
  inteira.
- **Sem consulta hoje**: `Nenhuma consulta marcada para hoje. Marque uma na agenda.`
- **Acompanha o relógio**: o "agora" vem do `Painel` e muda a cada minuto; a próxima é recalculada sem recarregar.

## Movimento e micro-interações

Cartão de vidro que sobe ao entrar na tela. No celular a pílula da próxima consulta fica na linha do rótulo, para o
nome caber e quebrar em vez de truncar.

## Histórico de mudanças

- [[2026-09-30-pr-170-painel-hoje]] — o bloco nasce: a lista do dia com a situação e a próxima em destaque.
