---
tipo: funcionalidade
camada: frontend
area: Documentos
rota: /documentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, documentos, declaracao, impressao]
---

# Declaração de comparecimento

## O que é

O terceiro documento da tela **Documentos** ([[Documentos]]): paciente, profissional, a data e o horário de início e de
fim, que podem vir de uma consulta do paciente, impressos numa folha com o cabeçalho da clínica e a linha para
assinar à mão (item 12.4 do [[2026-09-30-plano-da-v1]]). A v1 é demonstração, não prontuário: não há assinatura
digital nem validade jurídica. Ao contrário do [[Atestado]], que só tem campos, aqui a folha traz uma frase, composta
só do que veio dos campos: `Declaro que <nome> compareceu a esta clínica no dia <data>, das <início> às <fim>.`

## Onde está no código

- `src/modulos/documentos/Declaracao.tsx` — o cartão e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/declaracao.ts` — a regra pura: `camposDaDeclaracao`, `consultasDoPaciente`,
  `horarioDaConsulta`, `prepararDeclaracao`.
- `src/modulos/documentos/validacao.ts` — `resolverEscolha`, `dataValida` e `horaValida`.
- `src/modulos/documentos/PacienteEProfissional.tsx`, `Selecao.tsx` — as escolhas, as mesmas do [[Receituario]].
- `src/modulos/agenda/horarios.ts` (`emMinutos`, `emHora`) e `src/modulos/agenda/situacao.ts`
  (`ROTULO_DA_SITUACAO`) — o fim da consulta e o nome da situação.
- `src/componentes/FolhaImpressa.tsx`, `src/componentes/useImpressao.ts` — a folha e o mecanismo de impressão
  ([[FolhaImpressa]]).

## Comportamento

- **Campos**: `Paciente`, `Profissional` (só os ativos, de [[Profissionais]]), `Consulta do paciente` (opcional),
  `Data` (hoje ao abrir), `Hora de início` e `Hora de fim` (vazias).
- **Consulta do paciente**: só depois de escolher o paciente. Lista as consultas dele, da mais recente para a mais
  antiga, com o dia, o horário e a situação (`30/09/2026 · 14:30 às 15:15 · Confirmada`). **Falta e consulta
  cancelada não aparecem** (o paciente não compareceu, ou não houve consulta); agendada, confirmada, em atendimento
  e concluída aparecem, porque a agenda pode estar atrás do consultório e quem assina decide. Escolher uma preenche
  a data e as horas (o fim é o início mais a duração; se passasse da meia-noite, fica em 23:59), e os três campos
  seguem editáveis. Trocar de paciente solta a consulta escolhida. Paciente sem consulta: a lista fica desabilitada e
  o horário se digita.
- **Imprimir** confere tudo: paciente e profissional ativo, uma data que exista, as duas horas e o **fim depois do
  início** (igual não vale; o erro vai para a hora de fim). Faltando algo, a impressão não abre.
- **A folha** tem: o cabeçalho da clínica (cadastro em [[Clinica]]); o título `Declaração de comparecimento`; a
  frase da declaração; e, no fim, a linha para assinar à mão com o nome e o CRO do profissional. Entram só o nome do
  paciente e o dia e o horário: nada de CPF, telefone, procedimento nem situação da consulta.
- **Só a folha sai no papel**, em preto sobre branco também com o tema escuro. Conferida em A4: uma página, com a
  frase em duas linhas equilibradas (`text-balance`).
- **O formulário segue preenchido depois da impressão**, para imprimir outra via. Nada é gravado.

## Movimento e micro-interações

Nenhuma no cartão além das do kit (campos, botão). A folha não tem movimento.

## Limites conhecidos

- **A declaração é de um dia só**: início e fim no mesmo dia.
- **Consulta ainda por acontecer aparece na lista** (agendada, confirmada): o app não decide se o paciente já
  compareceu.
- **O procedimento da consulta não aparece** na lista nem na folha: só o dia, o horário e a situação (na lista).
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-169-documentos-declaracao]] — a declaração de comparecimento com o horário de início e de fim, que pode vir de uma consulta.
