---
tipo: adr
numero: 5
data: 2026-09-30
status: aceito
tags: [adr, dominio, dinheiro]
---

# ADR-005 — Dinheiro em centavos inteiros

## Contexto

O Arcada lida com dinheiro em vários pontos: preço do procedimento, item e desconto do orçamento,
parcelas, baixa, caixa do dia, relatório do mês e recibo. Em ponto flutuante, `0,1 + 0,2` não dá `0,3`:
somar reais em `number` erra centavo, e o erro aparece no caixa.

Parcelar também não fecha sozinho: R$ 100,00 em 3 parcelas de R$ 33,33 somam R$ 99,99 — falta um
centavo.

## Decisão

1. **Todo valor monetário é um número inteiro de centavos** (`number` inteiro), do cadastro ao
   relatório: preço, item, desconto, total, parcela, baixa e caixa.
2. **Real com vírgula só na borda**: formatação na saída (`R$ 1.234,56`) e interpretação da entrada
   (texto para centavos). No meio do caminho ninguém converte nem divide por 100.
3. **O parcelamento distribui o resto dos centavos nas primeiras parcelas**, um centavo para cada uma, de
   modo que a **soma das parcelas fecha exatamente o total**. Exemplo: R$ 100,00 (`10000` centavos) em 3
   parcelas dá `3334` + `3333` + `3333` = `10000`; R$ 100,01 (`10001`) dá `3334` + `3334` + `3333`.
4. A regra de dinheiro — conversão nas bordas e parcelamento — mora em `src/dominio/`, com teste.

## Consequências

**A favor**

- Soma e comparação exatas: "a soma das parcelas é igual ao total" vira uma igualdade de inteiros, fácil
  de testar.
- Nunca aparece `0,30000000000000004` na tela nem no impresso; o recibo com valor por extenso parte de
  dois inteiros — reais e centavos.

**Custos**

- **A borda é onde o bug mora.** Toda entrada de texto (`1.234,56`, `1234,5`, `1234`) passa por conversão,
  e cada formato precisa de teste. Todo `number` que chega da tela precisa ser inteiro; validar isso na
  escrita, em `src/dados/`, é o lugar natural (`TODO: confirmar nos PRs que gravam valor`).
- **Não existe fração de centavo.** Conta que produz fração — desconto percentual, reajuste de preços em
  lote — precisa de arredondamento explícito, uma vez, na função de domínio. A regra de arredondamento é
  `TODO: definir nos PRs do desconto e do reajuste em lote`.
- **O tipo não protege.** Um `number` em reais passado onde se esperam centavos passa no compilador; a
  defesa é o teste na borda.

Os termos (orçamento, parcela, baixa) estão em [[glossario]]; o fluxo do orçamento aprovado ao caixa, em
[[visao-de-produto]].

## Implementado em

- Conversão nas bordas e soma (`Centavos`, `formatarReais`, `paraCentavos`, `somarCentavos`):
  [[2026-09-30-pr-025-tipos-do-dominio]], em `src/dominio/dinheiro.ts`; comportamento em [[Dinheiro]].
- Parcelamento com distribuição do resto dos centavos: TODO: PR que implementar (item 7.5 do
  [[2026-09-30-plano-da-v1]]).
