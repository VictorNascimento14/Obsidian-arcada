---
tipo: aprendizado
data: 2026-09-30
contexto: PR #68 — desconto do orçamento
tags: [aprendizado, dinheiro, arredondamento]
---

# Percentual em ponto flutuante erra o meio centavo

Origem: [[2026-09-30-pr-068-tratamentos-desconto]].

## O sintoma

A conta óbvia do desconto, `Math.round((subtotal * percentual) / 100)`, devolve **um centavo a menos** quando o
resultado exato termina em meio centavo. R$ 50,00 a 0,57% são 28,5 centavos, que sobem para 29; o código
devolve 28. Só o empate exato aparece: nos outros casos o arredondamento absorve o ruído.

## A causa

0,57 não existe em binário. O `number` guarda algo como 0,56999999999999995, e `5000 * 0.57` dá
`2849.9999999999995`, não `2850`: o meio centavo fica um fio abaixo do meio e desce.

Medido, de 0,00% a 100,00% (passo 0,01) e subtotais de R$ 0,01 a R$ 200,00 (cerca de 200 milhões de
combinações): a conta em ponto flutuante diverge do arredondamento exato em **3.215** delas — por exemplo
R$ 110,00 a 0,35% (38,5 centavos, devolve 38), R$ 125,00 a 0,58% (72,5, devolve 72) e R$ 50,00 a 1,13%
(56,5, devolve 56).

## A correção

`aplicarDesconto` leva o percentual para **centésimos inteiros** (`Math.round(percentual * 100)`) e faz a
conta em inteiros: `Math.round(subtotal * centésimos / 10_000)`. Um empate dá `k + 0,5` exato, que o
`Math.round` sobe; fora do empate a distância até o meio é de pelo menos um décimo de milésimo. Na mesma
medição: **zero** divergências.

O preço é que o percentual vale com até duas casas decimais (33,333% vale 33,33%).

## A regra do desconto

Centavo mais próximo, **meio centavo sobe** (arredondamento comercial), uma vez, na função de domínio — é a
regra que o [[ADR-005-dinheiro-em-centavos-inteiros]] deixava em aberto para o desconto.

## Como evitar

- Não multiplique dinheiro por percentual em ponto flutuante: leve o percentual para inteiros antes da conta.
- Teste o empate, não só o caso redondo: R$ 50,00 a 0,57% e R$ 110,00 a 0,35% reprovam a conta ingênua. Estão
  em `desconto.test.ts`.
- Veja [[DescontoDoOrcamento]] para o comportamento completo.
