---
tipo: funcionalidade
camada: frontend
area: Painel
rota: /
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, painel, faturamento, financeiro]
---

# Faturamento por semana

## O que é

O bloco do [[PainelDoConsultorio]] que mostra como o caixa andou: o recebido em cada uma das últimas seis semanas, em
barras. A partir de `xl` fica ao lado das [[ConsultasDeHoje]]; abaixo disso, depois delas. Complementa os
[[IndicadoresDoMes]], que dão o total do mês num número só.

## Onde está no código

- `src/modulos/painel/FaturamentoPorSemana.tsx` — o bloco: cartão de vidro (região `Faturamento por semana`) com o
  título, o subtítulo e uma linha por semana.
- `src/modulos/painel/porSemana.ts` — `faturamentoPorSemana`, `pctDaBarra`, `degrauDaBarra` e `SEMANAS_NO_PAINEL`, a
  regra pura; o teste está em `porSemana.test.ts`.
- Lê `lancamentos` de `src/dados/colecoes.ts` e reaproveita `inicioDaSemana` e `somarDias` de `agenda/dias.ts`.

## Comportamento

- **As semanas**: as seis últimas, de segunda a domingo (a semana da agenda), da mais antiga à de hoje. A de hoje, ainda
  em curso, vem por último, em negrito e com `esta semana` ao lado do período. O período se escreve `28/09 a 04/10`
  (dia e mês, sem o ano).
- **O recebido** de uma semana é a soma dos valores das parcelas com `pagoEm` dentro dela, seja qual for o vencimento: a
  paga em atraso conta na semana em que o dinheiro entrou, e a em aberto não conta. Em centavos inteiros, escrito por
  `formatarReais`.
- **A barra** tem o tamanho do valor sobre o da maior semana (a maior enche o trilho) e a cor da escala
  `primary-800..500`, em quatro degraus de 25%: a mais forte é a mais escura, e abaixo de 25% fica a `primary-500`, a
  mais clara, porque um preenchimento mais claro some no trilho.
- **Sem recebimento** nas seis semanas: `Nenhuma parcela recebida nas últimas 6 semanas. Os valores aparecem aqui
  quando uma parcela recebe baixa.`, sem barras.
- **Leitor de tela**: o período e o valor são texto; a barra é só desenho, sem `role`.

## Movimento e micro-interações

Cada barra cresce da esquerda quando entra na tela (`MeterBar`), com atraso escalonado de linha em linha.

## Histórico de mudanças

- [[2026-09-30-pr-190-painel-faturamento]] — o bloco nasce: seis semanas de faturamento em barras.
