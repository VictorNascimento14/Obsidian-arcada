---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, orcamento, dinheiro]
---

# Desconto do orçamento

## O que é

A conta do desconto de um orçamento: quem monta o plano pede um percentual ou um valor, e o plano guarda o
desconto em centavos. Não tem tela — o orçamento e a impressão a usam. Parte do subtotal do plano
([[TotaisDoPlano]]) e segue o [[ADR-005-dinheiro-em-centavos-inteiros]].

## Onde está no código

- `src/modulos/tratamentos/desconto.ts` — `aplicarDesconto` e o tipo `Desconto`.
- `src/modulos/tratamentos/desconto.test.ts` — os testes.
- `src/modulos/tratamentos/plano.ts` — o `subtotal` de onde a conta parte.

## Comportamento

- **`aplicarDesconto(plano, desconto)`** devolve o plano com o `desconto` em centavos, sem mexer no original.
  O pedido é `{ tipo: "percentual", percentual }` (`10` é 10%) ou `{ tipo: "valor", valor }` (centavos).
- **Parte sempre do subtotal e substitui o desconto anterior**: aplicar duas vezes não soma. O plano guarda
  só os centavos, então mudar os itens depois não recalcula o desconto; quem reaplica é a tela.
- **Percentual**: sobre o subtotal, arredondado para o **centavo mais próximo; o meio centavo sobe**.
  R$ 100,00 a 10% dá R$ 10,00; R$ 33,33 a 10% dá R$ 3,33 (3,333); R$ 33,35 a 10% dá R$ 3,34 (3,335).
  O percentual vale com até duas casas decimais (12,5 ou 33,33); o que passa disso é arredondado antes da
  conta. A conta é toda em inteiros, para o empate do meio centavo não descer por erro de ponto flutuante:
  R$ 50,00 a 0,57% dá R$ 0,29 — ver [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]].
- **Nunca maior que o subtotal**: 150%, ou R$ 500 num plano de R$ 300, viram o subtotal, e o total do plano
  zera. Quem quiser avisar o usuário compara o desconto devolvido com o que foi digitado.
- **Nunca negativo**: menor que zero, ou o que não é número (campo vazio lido como `NaN`), vale `0`.
- **`valor` não é arredondado**: é `Centavos`, inteiro por contrato. A garantia é da entrada (`paraCentavos`)
  e da escrita em `src/dados/`.
- O `total(plano)` de [[TotaisDoPlano]] mostra o efeito: o subtotal menos o desconto.

## Histórico de mudanças

- [[2026-09-30-pr-068-tratamentos-desconto]] — `aplicarDesconto`, com o percentual arredondado em inteiros e o limite no subtotal.
