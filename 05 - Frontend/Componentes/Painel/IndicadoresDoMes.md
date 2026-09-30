---
tipo: funcionalidade
camada: frontend
area: Painel
rota: /
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, painel, indicadores, financeiro]
---

# Indicadores do mês

## O que é

O bloco do [[PainelDoConsultorio]] que resume o mês: o faturamento recebido, as consultas e a taxa de faltas, cada um
num cartão (`StatCard`) com o número animado. O título traz o mês por extenso (`Setembro de 2026`). Fica abaixo do
cartão de boas-vindas e acima das [[ConsultasDeHoje]].

## Onde está no código

- `src/modulos/painel/IndicadoresDoMes.tsx` — o bloco: uma `section` com o título e uma lista com um cartão por
  indicador.
- `src/modulos/painel/indicadores.ts` — `mesDe`, `faturamentoRecebido`, `consultasDoMes` e `taxaDeFaltas`, a regra
  pura; o teste está em `indicadores.test.ts`.
- Lê `consultas` e `lancamentos` de `src/dados/colecoes.ts`.

## Comportamento

- **O mês** é o de hoje (`AAAA-MM`), do relógio do painel: vira sozinho na virada do mês.
- **Faturamento recebido**: a soma dos valores das parcelas com `pagoEm` no mês, seja qual for o vencimento. A paga em
  atraso conta no mês em que o dinheiro entrou; a em aberto, mesmo vencida, não conta. Em centavos inteiros, escrito
  por `formatarReais`. Embaixo: `Parcelas pagas no mês`. Sem nenhuma, `R$ 0,00`.
- **Consultas no mês**: as do mês sem as canceladas (a cancelada libera o horário e não conta, como na agenda). As que
  ainda vão acontecer entram. Embaixo: `Sem as canceladas`.
- **Taxa de faltas**: faltou ÷ (concluída + faltou) no mês, com até uma casa (`12,5%`; `0%` e `100%` sem decimal).
  Agendada, confirmada, em atendimento e cancelada ficam fora da base. Sem concluída nem faltou não há base: o cartão
  mostra `—` e `Nenhuma consulta concluída ou com falta no mês`, em vez de `0%`.
- **Leitor de tela**: o número que anima fica `aria-hidden` e o valor final vai num par `sr-only` (invariante 10 do
  `CLAUDE.md` do repositório). O `—` tem o par `Sem dados`.

## Movimento e micro-interações

O número de cada cartão conta de zero até o valor quando o cartão entra na tela (`AnimatedNumber`), e o cartão levanta
ao passar o mouse (`StatCard`). Sob `prefers-reduced-motion`, o valor aparece direto.

## Histórico de mudanças

- [[2026-09-30-pr-177-painel-indicadores]] — o bloco nasce: faturamento recebido, consultas e taxa de faltas do mês.
