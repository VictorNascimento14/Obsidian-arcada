---
tipo: funcionalidade
camada: frontend
area: Painel
rota: /
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, painel, tratamentos, orcamento]
---

# Planos em aberto no painel

## O que é

O bloco do [[PainelDoConsultorio]] que diz quanto trabalho está em aberto: os orçamentos à espera da decisão do
paciente e os tratamentos aprovados ou em andamento, cada um num cartão com a contagem e o valor somado. Fica abaixo
dos [[IndicadoresDoMes]] e acima das [[ConsultasDeHoje]]. O link `Ver os planos` leva à lista completa
([[PlanosEmAberto]]).

## Onde está no código

- `src/modulos/painel/TratamentosEmAberto.tsx` — o bloco: uma `section` com o título, o link e uma lista de dois
  cartões.
- `src/modulos/painel/resumoEmAberto.ts` — `resumoEmAberto(planos, pacientes)`, a regra pura; o teste está em
  `resumoEmAberto.test.ts`.
- Lê `planos` e `pacientes` de `src/dados/colecoes.ts` e reaproveita `planosEmAberto` (`tratamentos/emAberto.ts`) e
  `total` (`tratamentos/plano.ts`, [[TotaisDoPlano]]).

## Comportamento

- **Em aberto** é o que `planosEmAberto` diz: proposto, aprovado e em andamento ([[SituacaoDoPlano]]). Concluído e
  recusado ficam de fora. A contagem é a mesma da lista `/tratamentos`.
- **Orçamentos em aberto**: os planos propostos, a proposta de valores que espera o paciente decidir. O cartão traz a
  contagem e, embaixo, `R$ X à espera da decisão do paciente`. Sem nenhum: `Nenhum orçamento à espera do paciente`.
- **Tratamentos em aberto**: os aprovados e os em andamento. O cartão traz a contagem e `R$ X aprovados ou em
  andamento`. Sem nenhum: `Nenhum tratamento aprovado ou em andamento`.
- **O valor** é a soma dos totais dos planos, já com o desconto (o que o paciente paga), em centavos. Não é o que ainda
  falta fazer.
- Plano de paciente que já não existe continua contando.
- Os dois grupos repartem os planos em aberto: a soma das contagens é a da lista.

## Movimento e micro-interações

A contagem conta de zero até o valor quando o cartão entra na tela (`AnimatedNumber`), e o cartão levanta ao passar o
mouse (`StatCard`). O valor em reais fica estável, em texto.

## Histórico de mudanças

- [[2026-09-30-pr-182-painel-em-aberto]] — o bloco nasce: orçamentos e tratamentos em aberto, com a contagem e o valor.
