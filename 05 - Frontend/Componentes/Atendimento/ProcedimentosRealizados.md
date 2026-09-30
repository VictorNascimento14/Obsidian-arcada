---
tipo: funcionalidade
camada: frontend
area: Atendimento
rota: /atendimento/:consultaId
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, atendimento, plano, tratamentos]
---

# Procedimentos realizados

## O que é

O cartão da tela do [[AtendimentoDaConsulta]] onde se registra o que foi feito na consulta: entre os itens dos planos
de tratamento do paciente, escolhe-se o que foi realizado e o item passa a ter o dia (`ItemPlano.realizadoEm`). É esse
campo que faz o [[ProgressoDoTratamento]] andar. Só aparece com a consulta em atendimento.

## Onde está no código

- `src/modulos/atendimento/ProcedimentosRealizados.tsx` — o cartão.
- `src/modulos/atendimento/realizados.ts` — `registrarRealizados` e `aceitaRegistro`, a regra e a gravação.
- `TelaDoAtendimento.tsx` — monta o cartão quando a consulta está `Em atendimento`.
- Reusa `transitar` de `src/modulos/tratamentos/situacao.ts` ([[SituacaoDoPlano]]), `detalheDoItem` e `SituacaoBadge` de
  `tratamentos`.

## Comportamento

- Mostra os planos do paciente que estão **aprovados ou em andamento**, cada um como `Plano N` (a posição entre todos os
  planos do paciente, como na aba Tratamentos) com a situação. Plano proposto, recusado ou concluído não entra.
- Cada item **pendente** tem uma caixa de escolha, com o nome do procedimento e o dente e as faces (`Dente 16 ·
  primeiro molar superior direito · face oclusal`). Item **já realizado** mostra `Realizado em DD/MM/AAAA`.
- **`Marcar como realizados`** fica desabilitado sem item escolhido. Ao clicar, grava `realizadoEm` com o **dia de hoje**
  (o do clique, e não o da consulta) em todos os escolhidos e limpa a escolha.
- **O primeiro item realizado leva o plano de `Aprovado` para `Em andamento`.** Depois disso o plano segue em andamento;
  o último item feito **não** conclui o plano — quem conclui é o botão `Concluir` da tela do plano
  ([[PlanoDeTratamento]]).
- `registrarRealizados` é tudo ou nada. Recusa, sem gravar nada: plano que não existe mais, plano fora de
  aprovado/em andamento, nenhum item escolhido, item que saiu do plano, item que já estava realizado (a data antiga
  nunca é trocada) e data fora do formato `AAAA-MM-DD`. O motivo sai num aviso.
- Sem plano aprovado ou em andamento, o cartão explica e leva à ficha do paciente.
- O item guarda **só o dia**, não quem fez: `ItemPlano` não tem campo para o profissional. Ele é o da consulta, que a
  tela mostra.

## Histórico de mudanças

- [[2026-09-30-pr-143-atendimento-realizado]] — o cartão e a gravação do procedimento realizado nos itens do plano.
