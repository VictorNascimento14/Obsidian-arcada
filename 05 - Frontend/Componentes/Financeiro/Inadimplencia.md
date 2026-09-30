---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: /financeiro
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, inadimplencia, parcela]
---

# Inadimplência

## O que é

A regra que diz quem deve e há quanto tempo — por paciente, as parcelas vencidas e sem baixa, o total vencido e os dias
de atraso — e o cartão **Inadimplência** da tela `/financeiro`, que a mostra. Inadimplência é, no [[glossario]], a
situação de uma parcela cujo vencimento passou sem baixa: no código, a parcela `vencida` de [[SituacaoDaParcela]].

## Onde está no código

- `src/modulos/financeiro/inadimplencia.ts` — `diasDeAtrasoDaParcela(parcela, hoje)`, `inadimplentes(lancamentos,
  pacientes, hoje)` e o tipo `Inadimplente`.
- `Inadimplentes.tsx` — o cartão. `PaginaFinanceiro.tsx` o monta entre as contas a receber ([[ContasAReceber]]) e os
  planos sem parcelas ([[PlanosSemParcelas]]).

## Comportamento

- **Quem conta**: só a parcela em aberto com o vencimento antes de hoje. A que vence hoje ainda não é atraso, a a
  vencer não venceu e a paga saiu da conta. Paciente sem parcela vencida não entra.
- **Por paciente**: `parcelas` (quantas vencidas), `totalVencido` (a soma dos valores, em centavos) e `diasDeAtraso`
  — os dias da parcela vencida mais antiga, isto é, há quanto tempo ele deve. Uma parcela vencida mais recente não
  abrevia esse número.
- **Os dias** são dias de calendário entre duas datas `AAAA-MM-DD`, pelo número do dia em UTC (`Date.UTC` com
  números): o fuso não mexe na conta, e virada de mês, virada de ano e ano bissexto saem certos (de 28/02 a 01/03 são
  2 dias em 2028 e 1 dia em 2027).
- **Ordem**: do atraso mais antigo ao mais recente; no empate, o maior total vencido e, por fim, o nome (pt-BR).
  Parcela de paciente que já não existe entra sem `paciente` (`Paciente não encontrado`).
- **O cartão**: o título, o resumo `2 pacientes inadimplentes, com R$ 65,00 vencidos` (ou `1 paciente inadimplente`)
  e uma linha por paciente — avatar, nome, `2 parcelas vencidas · 30 dias de atraso` e o total vencido. Sem ninguém:
  `Nenhum paciente inadimplente.` O dia de hoje é o de quando a tela é desenhada, como nas contas a receber.

## Movimento e micro-interações

Cartão de vidro, sem animação própria. No celular, o total fica à direita do nome e do texto de atraso.

## Histórico de mudanças

- [[2026-09-30-pr-157-financeiro-inadimplencia]] — a regra `inadimplentes` e o cartão Inadimplência em `/financeiro`.
