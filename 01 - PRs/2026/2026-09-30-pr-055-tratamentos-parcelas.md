---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 55
url: https://github.com/VictorNascimento14/Arcada/pull/55
branch: feat/tratamentos-parcelas
tags: [pr, tratamentos, orcamento, dinheiro]
status: aberto
---

# PR #55 — feat(tratamentos): parcelar o orçamento distribuindo o resto dos centavos

## 🎯 Contexto

Item 7.5 (Parcelamento com distribuição de centavos) do [[2026-09-30-plano-da-v1]], módulo 7. É a regra que o [[ADR-005-dinheiro-em-centavos-inteiros]] pede; o financeiro (10.1) usa a função ao aprovar o plano, e a impressão do orçamento (7.7) mostra as parcelas. Fecha a issue #50.

## 🔧 Mudanças

- `src/modulos/tratamentos/parcelas.ts` — `parcelar` e o tipo `Parcela` (`valor` e `vencimento`, tirados de `Lancamento`).
- `src/modulos/tratamentos/parcelas.test.ts` — os exemplos do ADR, a soma exata em muitas combinações, os vencimentos (dia 31, fevereiro, ano bissexto, virada de ano) e a entrada inválida.

## 🧠 Decisões técnicas

- **A regra mora no módulo Tratamentos, não em `src/dominio/`.** O ponto 4 do ADR-005 diz `src/dominio/`; o backlog põe em `src/modulos/tratamentos/parcelas.ts`, e o financeiro importa daqui. Se outro módulo passar a usar, mover para `src/dominio/` é trocar o import.
- **O resto dos centavos vai um para cada parcela, nas primeiras.** `resto = total % n`; as `resto` primeiras levam `base + 1`. A base sai de `(total - resto) / n`, um múltiplo exato de `n`, então a divisão não arredonda nem perto do maior inteiro seguro.
- **Cada vencimento parte do dia do primeiro, não do anterior.** Partindo do anterior, 31/01 viraria 28/02 e depois 28/03. Assim o dia 31 volta ao 31 nos meses que o têm.
- **A primeira parcela vence na própria data dada**, e não um mês depois: quem quer entrada mais parcelas escolhe a data.
- **Sem `Date`.** A data é conferida por regex e pelos dias do mês, com o ano bissexto pela regra gregoriana. `new Date("2026-01-31")` lê UTC e, no fuso do Brasil, recuaria um dia.
- **Entrada inválida lança `RangeError`** (`n`, total ou vencimento), como `idade` e `valorPorExtenso`: os valores vêm de estado do app ou de campos que a tela já validou, e um erro alto vale mais que parcelas erradas.
- **Total menor que `n` deixa as últimas parcelas em `0`, e total `0` dá parcelas zeradas.** `parcelar` não decide se isso é aceitável: quem gera os lançamentos decide.

## ⚠️ Armadilhas e aprendizados

- Somar um mês a partir do vencimento anterior arrasta o dia (31/01, 28/02, 28/03…). O teste do dia 31 fixa o comportamento certo.
- Fevereiro de 2100 tem 28 dias (século que não é múltiplo de 400) e o de 2000 teve 29: a regra gregoriana está nos testes.

## 🧪 Como testar

1. `npm test` → `parcelas.test.ts` passa: os exemplos do ADR-005 (10000 em 3 dá 3334 + 3333 + 3333; 10001 dá 3334 + 3334 + 3333) e a soma exata para centenas de totais, até o maior inteiro seguro, em 1 a 24 parcelas.
2. Ainda em `parcelas.test.ts`, os vencimentos: de 2026-01-31, cinco parcelas vencem em 31/01, 28/02, 31/03, 30/04 e 31/05 (o dia não se arrasta); fevereiro de 2028 tem 29 dias e o de 2100, 28.
3. A entrada inválida (`n` zero ou fracionário, total negativo ou fracionário, vencimento que não existe, como 2026-02-30) lança `RangeError`.
4. `npm run lint && npm run type-check && npm run build` passam.

## 📎 Documentação afetada

- [[ParcelamentoDoOrcamento]]
- [[Dinheiro]]
- [[ADR-005-dinheiro-em-centavos-inteiros]]
- [[2026]] (changelog)
