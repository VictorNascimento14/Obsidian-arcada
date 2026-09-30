---
tipo: funcionalidade
camada: frontend
area: Comuns
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, comuns, dinheiro]
---

# Dinheiro

## O que é

O dinheiro do Arcada: `Centavos`, um número inteiro (R$ 12,34 é `1234`), e as funções das bordas — `formatarReais`
na saída, `paraCentavos` na entrada — mais `somarCentavos`. No meio do caminho ninguém converte nem divide por
100. A decisão está em [[ADR-005-dinheiro-em-centavos-inteiros]]. O parcelamento, que também mora aqui pelo ADR,
ainda não existe: vem no item 7.5 do [[2026-09-30-plano-da-v1]].

## Onde está no código

- `src/dominio/dinheiro.ts` — `Centavos`, `formatarReais`, `paraCentavos` e `somarCentavos`.
- `src/dominio/dinheiro.test.ts` — os formatos aceitos e recusados, o limite do inteiro exato e a soma.

## Comportamento

- **`formatarReais(123456)` devolve `R$ 1.234,56`**, pelo `Intl.NumberFormat("pt-BR")` em BRL. O espaço depois do
  `R$` é o não separável (U+00A0), não o comum — ver [[2026-09-30-intl-separa-o-real-com-espaco-nao-separavel]].
- **`paraCentavos(texto)` lê a entrada brasileira e devolve os centavos, ou `null` se o texto não for um valor:**

  | Texto | Resultado |
  |---|---|
  | `1.234,56` · `1234,56` | `123456` |
  | `12` | `1200` (R$ 12,00) |
  | `12,5` | `1250` (R$ 12,50) |
  | `R$ 3,00` · `R$3,00` · `R$` + U+00A0 + `3,00` | `300` |
  | `12.50` · `0.500` · `12,345` · `-5` · `,50` · `12,` · vazio · texto solto | `null` |
  | mais que 9.007.199.254.740.991 centavos (o maior inteiro exato) | `null` |

  O ponto é sempre milhar: `12.500` são R$ 12.500,00, e `12.50` não é valor. Sinal é recusado porque preço,
  desconto e pagamento não são negativos. O que `formatarReais` escreve volta ao mesmo número.
- **`somarCentavos(...valores)` soma inteiros**; sem argumentos, `0`. Onde a soma de reais em ponto flutuante erra
  (`0,1 + 0,2`), a de centavos fecha (`10 + 20`).
- **A conversão não valida a escrita.** O tipo é só `number`: quem grava um valor fracionário ou em reais passa no
  compilador. A defesa é o teste na borda e a validação em `src/dados/`.

## Histórico de mudanças

- [[2026-09-30-pr-025-tipos-do-dominio]] — `Centavos`, `formatarReais`, `paraCentavos` e `somarCentavos`.
