---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 49
url: https://github.com/VictorNascimento14/Arcada/pull/49
branch: feat/tratamentos-plano
tags: [pr, tratamentos, orcamento]
status: merged
---

# PR #49 — feat(tratamentos): calcular o subtotal, o total e os itens realizados do plano

## 🎯 Contexto

Item 7.1 (Modelo e totais) do [[2026-09-30-plano-da-v1]], módulo 7 (Plano de tratamento e orçamento). Os itens seguintes — desconto (7.4), parcelamento (7.5), impressão (7.7) e progresso (7.8) — e o financeiro (10.1) leem o total e os itens realizados daqui. Fecha a issue #44.

## 🔧 Mudanças

- `src/modulos/tratamentos/plano.ts` — `subtotal`, `total` e `itensRealizados`, com teste em `plano.test.ts`.
- `src/dominio/tratamento.ts` — `ItemPlano.realizadoEm?: DataISO`, opcional, no molde de `Lancamento.pagoEm`.

## 🧠 Decisões técnicas

- **O item marca a realização com o dia (`realizadoEm`), não com um booleano.** Segue o `pagoEm` do lançamento (ausente é em aberto) e guarda quando foi feito sem um segundo campo. É opcional: plano e semente que já existem continuam válidos. Quem grava o dia é o atendimento (9.2); aqui só se lê.
- **O campo novo mexe num tipo compartilhado (`src/dominio/`).** A mudança é aditiva — uma linha opcional —, e sem ela o plano não teria de onde tirar o progresso.
- **O total nunca é negativo.** O desconto é um valor fixo em centavos; se um item sair do plano depois, o desconto pode passar do subtotal. `total` desconta no máximo o subtotal, em vez de inverter o sinal.
- **`itensRealizados` devolve a lista, não a contagem.** O progresso é `itensRealizados(plano).length` sobre `plano.itens.length`, e a ficha pode listar os feitos.

## ⚠️ Armadilhas e aprendizados

- O plano guarda o desconto em centavos, não o percentual: mudar os itens depois não recalcula o desconto, e `total` só o limita ao subtotal.

## 🧪 Como testar

1. `npm test` → `plano.test.ts` passa: subtotal em centavos (com e sem itens), total com o desconto, total zerado quando o desconto é igual ou maior que o subtotal e os itens realizados na ordem do plano.
2. `npm run type-check` → `ItemPlano.realizadoEm` é opcional: o código que já cria itens sem o campo continua compilando.
3. `npm run lint && npm run build` passam.

## 📎 Documentação afetada

- [[TotaisDoPlano]]
- [[TiposDoDominio]]
- [[2026]] (changelog)
