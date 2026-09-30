---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 133
url: https://github.com/VictorNascimento14/Arcada/pull/133
branch: feat/agenda-remarcar
tags: [pr, agenda, remarcar, cancelar]
status: merged
---

# PR #133 — feat(agenda): remarcar e cancelar a consulta com motivo

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.9, em cima do detalhe da consulta do [[2026-09-30-pr-118-agenda-situacao-tela]]. Fecha a issue #124.

## 🔧 Mudanças

- `marcar.ts`: `remarcarConsulta` (regrava a consulta pelo id, com a mesma conferência de `marcarConsulta`, que virou a função `barrar`), `camposDaRemarcacao` e o parâmetro `ignorar` em `restricoesDaAgenda` e `horariosSugeridos`.
- `MarcarConsulta.tsx`: a prop `remarcar`. O mesmo formulário abre com os dados da consulta, com o paciente travado, título `Remarcar consulta` e botão `Remarcar`.
- `mudarSituacao.ts`: `cancelarConsulta` (motivo obrigatório, até `MOTIVO_MAX` = 200 caracteres); `transitar` é a parte comum com `mudarSituacao`.
- `situacao.ts`: `aguardaAtendimento` (agendada ou confirmada). `src/dominio/consulta.ts`: o campo opcional `motivoCancelamento`.
- `DetalheDaConsulta.tsx` e `PaginaAgenda.tsx`: os botões Remarcar e Cancelar consulta, o campo do motivo, e a página que troca o detalhe pelo formulário.
- Testes: 33 novos, em `situacao.test.ts`, `mudarSituacao.test.ts`, `marcar.test.ts` e `PaginaAgenda.test.tsx`.

## 🧠 Decisões técnicas

- A própria consulta sai do conflito pelo id: `ignorar` vira o `id` da candidata em `conflitosDaConsulta`, que já tinha esse mecanismo. Vale para o conflito, para as sugestões de horário e para a gravação, sem cada chamador filtrar a lista.
- A consulta confirmada, remarcada, volta a agendada: o paciente confirmou o horário antigo, não o novo. Não é uma transição de `situacao.ts` (que não deixa voltar): é a regra de `remarcarConsulta`, e o formulário avisa antes de gravar.
- Só se remarca a consulta que aguarda o atendimento (`aguardaAtendimento`), e o paciente não muda: outro paciente é outra consulta.
- O detalhe fecha e o formulário de marcar abre no lugar. Dois `Modal` empilhados brigariam pelo Esc e pelo foco.
- `cancelarConsulta` é a regra do motivo (obrigatório, sem os espaços das pontas, até 200); o campo da tela só limita a digitação com `maxLength`. `mudarSituacao` continua recusando `cancelada`.
- `ResultadoDaMarcacao` ganha `erro?`, para a consulta inteira barrada (já não existe, ou já não se remarca). A tela avisa por toast e fecha, porque não há campo a corrigir.
- O campo do motivo entra no lugar dos botões e recebe o foco (`autoFocus`): o botão que o abriu sai do ar, e sem isso o foco se perderia.

## ⚠️ Armadilhas e aprendizados

- Sem `ignorar`, a consulta conflita com o próprio horário antigo: remarcar 15 minutos para a frente daria conflito.
- Remarcar sem procedimento tem de apagar o `procedimentoId` antigo: o `{ ...atual, ... }` sozinho o manteria. `remarcarConsulta` o remove quando o formulário vem vazio.
- Consulta cujo profissional ou cadeira ficou inativo: o formulário só lista os ativos, então o campo aparece vazio e a gravação pede um ativo. Um teste cobre o profissional.
- O motivo é gravado, mas nenhuma tela o mostra ainda: a cancelada sai da grade. Ele aparece quando houver uma lista de consultas canceladas, como a da ficha do paciente.

## 🧪 Como testar

1. Rode `npm run dev`, abra `/agenda` e clique no cartão de uma consulta agendada: o detalhe mostra Remarcar e Cancelar consulta.
2. Clique em Remarcar: abre `Remarcar consulta` com os dados da consulta e o paciente desabilitado. Troque o início por outro horário livre e clique em Remarcar: o cartão muda de lugar e aparece o aviso `Consulta remarcada`.
3. Remarque uma consulta para um horário da mesma cadeira que já tem outra: aparece a mensagem em vermelho e o botão fica desabilitado. Remarcar para um horário que se sobrepõe ao da própria consulta é permitido.
4. Remarque uma consulta confirmada: o formulário avisa que ela volta para agendada, e o cartão passa a `Agendada`.
5. Clique em Cancelar consulta: aparece o campo do motivo. Confirmar cancelamento vazio mostra `Informe o motivo do cancelamento.`; com motivo, a consulta some da grade.
6. Rode `npx vitest run --maxWorkers=2 src/modulos/agenda`.

## 📎 Documentação afetada

- [[RemarcarECancelarConsulta]]
- [[DetalheDaConsulta]]
- [[MarcarConsulta]]
- [[AgendaDoDia]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
