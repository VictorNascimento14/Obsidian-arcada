---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, orcamento, dinheiro]
---

# Parcelamento do orçamento

## O que é

A conta que divide o total de um orçamento em parcelas e dá o vencimento de cada uma. Não tem tela — o
financeiro a usa ao aprovar um plano, e a impressão do orçamento mostra o resultado. É a regra que o
[[ADR-005-dinheiro-em-centavos-inteiros]] pede: a soma das parcelas fecha exatamente o total.

## Onde está no código

- `src/modulos/tratamentos/parcelas.ts` — `parcelar` e o tipo `Parcela`.
- `src/modulos/tratamentos/parcelas.test.ts` — os testes.

## Comportamento

- **`parcelar(total, n, primeiroVencimento)`** devolve `n` parcelas, cada uma com `valor` (centavos) e
  `vencimento` (`AAAA-MM-DD`) — o que `Lancamento` tem de próprio; quem gera o lançamento acrescenta o
  id, o paciente e o plano.
- **Valores**: todas recebem o mesmo tanto e o resto dos centavos vai, um para cada, às primeiras.
  R$ 100,00 em 3 dá R$ 33,34 + R$ 33,33 + R$ 33,33; R$ 100,01 dá R$ 33,34 + R$ 33,34 + R$ 33,33. A soma é
  exatamente o total, e nenhuma parcela passa de outra por mais de 1 centavo.
- **A primeira parcela vence na data dada**; as outras, a cada mês.
- **Cada vencimento parte do dia do primeiro, não do anterior.** De 31/01: 31/01, 28/02, 31/03, 30/04 — o
  dia 31 recua nos meses curtos e volta nos que o têm, em vez de se arrastar para o 28. Fevereiro tem 29
  dias nos anos bissextos (2028, 2000) e 28 nos demais (2100).
- **Total menor que `n`** deixa as últimas parcelas em `0`, e total `0` dá parcelas zeradas: quem gera os
  lançamentos decide se aceita.
- **Entrada inválida lança `RangeError`**: `n` que não seja inteiro a partir de 1, total que não seja
  inteiro seguro não negativo, vencimento fora do formato ou de um dia que não existe (30/02, 29/02 em
  ano não bissexto).
- A conta é por número e texto, sem `Date`: `new Date("2026-01-31")` lê UTC e, no fuso do Brasil,
  recuaria um dia.

## Histórico de mudanças

- [[2026-09-30-pr-055-tratamentos-parcelas]] — `parcelar`, com o resto dos centavos nas primeiras parcelas e os vencimentos mensais.
