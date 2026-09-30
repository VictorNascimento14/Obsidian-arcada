---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 68
url: https://github.com/VictorNascimento14/Arcada/pull/68
branch: feat/tratamentos-desconto
tags: [pr, tratamentos, orcamento, dinheiro]
status: aberto
---

# PR #68 — feat(tratamentos): aplicar o desconto percentual ou em valor ao orçamento

## 🎯 Contexto

Item 7.4 (Desconto) do [[2026-09-30-plano-da-v1]], módulo 7. Usa o `subtotal` do item 7.1 ([[TotaisDoPlano]]) e define a regra de arredondamento que o [[ADR-005-dinheiro-em-centavos-inteiros]] deixava como `TODO`. Fecha a issue #63.

## 🔧 Mudanças

- `src/modulos/tratamentos/desconto.ts` — `aplicarDesconto` e o tipo `Desconto` (percentual ou valor).
- `src/modulos/tratamentos/desconto.test.ts` — arredondamento, empates de meio centavo, limites, substituição do desconto anterior e uma varredura que confere que o desconto é inteiro e fica entre zero e o subtotal.

## 🧠 Decisões técnicas

- **O centavo mais próximo, com o meio centavo subindo** (arredondamento comercial), uma vez, na função de domínio (ADR-005). O backlog dizia só "centavo mais próximo"; o empate é decisão deste PR.
- **A conta é em inteiros:** o percentual vira centésimos (`Math.round(percentual * 100)`) e o desconto sai de `Math.round(subtotal * centésimos / 10_000)`. Em ponto flutuante, `subtotal * percentual / 100` erra o empate do meio centavo (3.215 divergências em 200 milhões de combinações medidas); em inteiros, nenhuma. O preço é o percentual valer com até duas casas decimais.
- **Limitar, não recusar.** O que passa do subtotal (150%, R$ 500 num plano de R$ 300) vira o subtotal; negativo e `NaN` viram `0`.
- **Parte do subtotal e substitui o desconto anterior.** Aplicar o mesmo percentual duas vezes não soma, e mudar os itens depois não recalcula: quem reaplica é a tela.
- **`valor` não é arredondado nem conferido como inteiro:** é `Centavos`, e a garantia é da entrada (`paraCentavos`) e da escrita em `src/dados/`, como no resto do dinheiro.

## ⚠️ Armadilhas e aprendizados

- `subtotal * percentual / 100` parece certo e erra o empate do meio centavo — 0,57% de R$ 50,00 dá 28 centavos em vez de 29. O teste dos empates fixa o comportamento certo; o caso está em [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]].
- Percentual com mais de duas casas decimais é arredondado antes da conta (33,333% vale 33,33%).

## 🧪 Como testar

1. `npm test` → `desconto.test.ts` passa: percentual arredondado ao centavo mais próximo (333,3 desce; 333,9 e 333,5 sobem), desconto em valor, limite no subtotal e zero para negativo ou não numérico.
2. Ainda em `desconto.test.ts`, os empates que o ponto flutuante erra: R$ 50,00 a 0,57% dá 29 centavos (28,5 exato) e R$ 110,00 a 0,35% dá 39 — a conta `subtotal * percentual / 100` reprovaria.
3. Aplicar um desconto a um plano que já tinha outro substitui o anterior; o `total` de `plano.ts` mostra o efeito.
4. `npm run lint && npm run type-check && npm run build` passam.

## 📎 Documentação afetada

- [[DescontoDoOrcamento]]
- [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]]
- [[ADR-005-dinheiro-em-centavos-inteiros]]
- [[TotaisDoPlano]]
- [[2026]] (changelog)
