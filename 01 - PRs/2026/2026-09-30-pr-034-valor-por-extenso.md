---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 34
url: https://github.com/VictorNascimento14/Arcada/pull/34
branch: feat/financeiro-extenso
tags: [pr, financeiro, recibo]
status: aberto
---

# PR #34 — feat(financeiro): escrever o valor do recibo por extenso

## 🎯 Contexto

Módulo 10 (Financeiro) do [[2026-09-30-plano-da-v1]], item 10.7 — só a parte pura; a impressão do recibo vem depois. Fecha a issue #32.

## 🔧 Mudanças

- `src/modulos/financeiro/extenso.ts` — `valorPorExtenso(centavos)`.
- `src/modulos/financeiro/extenso.test.ts` — os quatro exemplos do item, centavos e zero, até 999, milhares, milhão em diante, o maior inteiro seguro e as entradas recusadas.

## 🧠 Decisões técnicas

- Entrada em centavos inteiros ([[ADR-005-dinheiro-em-centavos-inteiros]]). O que não é inteiro seguro e não negativo lança `RangeError`: num documento de dinheiro, quebrar é melhor que escrever um valor diferente do que foi cobrado.
- O "e" entre os grupos vai só antes do último grupo diferente de zero, e só quando ele é menor que 100 ou centena redonda: "mil e um", "dois mil e quinhentos", "um milhão e cem mil", mas "mil cento e um" e "um milhão duzentos e trinta e quatro mil quinhentos e sessenta e sete".
- "de reais" quando o valor em reais termina em milhão, bilhão ou trilhão redondo ("um milhão de reais", "três bilhões e um milhão de reais"); "um milhão e quinhentos mil reais" não leva.
- Singular só para 1 ("um real", "um centavo"); o zero também fica no singular ("zero real"). Sem reais, saem só os centavos ("cinquenta centavos"); com os dois, ligados por "e".
- Vai até os trilhões, o que cobre todo inteiro seguro em centavos (até R$ 90 trilhões).
- Os grupos de três dígitos e a divisão por 100 usam divisão sobre múltiplo exato (`n - n % d`), sem depender do arredondamento de `Math.floor` sobre quociente em ponto flutuante.

## ⚠️ Armadilhas e aprendizados

- O `number` não diz a unidade: `valorPorExtenso(120)` escreve "um real e vinte centavos". Só o fracionário é recusado; quem chama tem de passar centavos, como manda a ADR-005.
- Mil é "mil reais", nunca "um mil": o "um" cai só na classe do mil; de milhão em diante ele fica ("um milhão").
- O zero sai como "zero real" só para a função valer para todo inteiro não negativo; se um recibo de valor zero faz sentido é decisão da impressão, que vem depois.

## 🧪 Como testar

1. `npm test -- src/modulos/financeiro` → `extenso.test.ts`: os quatro exemplos do item, centavos e zero, até 999, milhares, milhão em diante, o maior inteiro seguro e as entradas recusadas.
2. Conferir à mão um valor com os dois grupos: `valorPorExtenso(123456789)` → "um milhão duzentos e trinta e quatro mil quinhentos e sessenta e sete reais e oitenta e nove centavos".

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[ADR-005-dinheiro-em-centavos-inteiros]]
- [[2026]] (changelog)
