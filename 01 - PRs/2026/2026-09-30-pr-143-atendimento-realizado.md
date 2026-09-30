---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 143
url: https://github.com/VictorNascimento14/Arcada/pull/143
branch: feat/atendimento-realizado
tags: [pr, atendimento, tratamentos, plano]
status: merged
---

# PR #143 — feat(atendimento): registrar o procedimento realizado nos itens do plano

## 🎯 Contexto

Item 9.2 (Registrar procedimento realizado) do [[2026-09-30-plano-da-v1]]: escolher itens do plano do paciente e marcar realizados. Fecha a issue #140.

## 🔧 Mudanças

- `src/modulos/atendimento/realizados.ts` (+ teste): `registrarRealizados(planoId, itemIds, dia)` e `aceitaRegistro`. Devolve `{ ok, plano }` ou `{ ok: false, erro }`, como `mudarSituacao` da agenda.
- `ProcedimentosRealizados.tsx` (+ teste): o cartão com os itens por plano, as caixas de escolha, o botão e a data dos já feitos.
- `TelaDoAtendimento.tsx`: mostra o cartão quando a consulta está em atendimento.

## 🕵️ Dado pessoal (LGPD)

Grava no plano do paciente só o dia em que o procedimento foi feito (dado de saúde, guardado no repositório local); nada é enviado para fora.

## 🧠 Decisões técnicas

- A regra mora em `registrarRealizados`, não no botão: só o plano `aprovado` ou `em-andamento` recebe registro; o primeiro item feito chama `transitar(plano, "em-andamento")` de `tratamentos/situacao`.
- É tudo ou nada: item que não existe mais, item já realizado ou data fora do formato recusam o registro inteiro, e um item já realizado nunca muda de data.
- A data é o dia do clique (`diaISO(new Date())`), e não o da consulta: o registro é do momento em que o procedimento é feito.
- `ItemPlano` guarda só `realizadoEm`, sem o profissional. O profissional é o da consulta, que a tela mostra, e o da evolução clínica (9.3); guardá-lo por item pede um campo novo em `src/dominio` (`ponytail:` em `realizados.ts`).
- O último item feito não conclui o plano: quem conclui é o botão `Concluir` da tela do plano (`ponytail:` em `realizados.ts` diz onde automatizar).

## ⚠️ Armadilhas e aprendizados

- O número `Plano N` do cartão é a posição entre **todos** os planos do paciente, como na aba Tratamentos: pode pular (Plano 1, Plano 3) quando há um plano proposto no meio.

## 🧪 Como testar

1. Com um paciente que tenha um plano **aprovado** (aba Tratamentos), inicie o atendimento de uma consulta dele (aba Atendimentos).
2. Na tela do atendimento, escolha um ou mais itens e clique em **Marcar como realizados**: cada item escolhido passa a mostrar **Realizado em** com o dia de hoje e perde a caixa de escolha.
3. Volte à aba **Tratamentos** da ficha: o plano está **Em andamento** e o progresso avançou.
4. Plano proposto, recusado ou concluído não aparece no cartão; sem plano aprovado, o cartão explica e leva à ficha.
5. Com a consulta ainda agendada ou já concluída, o cartão não aparece.

## 📎 Documentação afetada

- [[AtendimentoDaConsulta]]
- [[ProgressoDoTratamento]]
- [[SituacaoDoPlano]]
- [[ProcedimentosRealizados]]
- [[2026]] (changelog)
