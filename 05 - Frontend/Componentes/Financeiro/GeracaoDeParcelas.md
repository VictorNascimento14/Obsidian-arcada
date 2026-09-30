---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, parcela, orcamento, dinheiro]
---

# Geração de parcelas

## O que é

As regras que levam um plano aprovado às parcelas: quais planos esperam a geração, a conta das parcelas a partir do
que o formulário digita e a escrita que grava os lançamentos. A divisão em si é o [[ParcelamentoDoOrcamento]]
(`parcelar`), em centavos inteiros ([[ADR-005-dinheiro-em-centavos-inteiros]]). Quem usa é a tela
[[PlanosSemParcelas]].

## Onde está no código

- `src/modulos/financeiro/aParcelar.ts` — `SITUACOES_QUE_PARCELAM` (`aprovado` e `em-andamento`) e
  `planosParaParcelar(planos, lancamentos, pacientes)`.
- `src/modulos/financeiro/lancamentos.ts` — `calcularParcelas`, `gerarParcelas`, `MAXIMO_DE_PARCELAS` (60) e os
  tipos `CamposDasParcelas` e `ErrosDasParcelas`.

## Comportamento

- **`planosParaParcelar`**: os planos aprovados ou em andamento cujo id não aparece em nenhum `planoId` da coleção
  `lancamentos`, cada um com o seu paciente, por nome (pt-BR).
- **`calcularParcelas(plano, campos)`** não grava. Lê o número de parcelas (só dígitos, de 1 a 60), confere que cada
  parcela terá ao menos 1 centavo e chama `parcelar(total(plano), n, vencimento)`, com o total já com o desconto
  ([[TotaisDoPlano]]). Devolve `{ parcelas }` ou `{ erros }` por campo (`parcelas`, `vencimento`). O `parcelar` deixa
  as últimas parcelas em `0` quando o total é menor que `n` e diz que quem parcela decide: aqui, não se aceita.
- **`gerarParcelas(planoId, campos)`** devolve os erros de `calcularParcelas` (objeto vazio quer dizer que gravou) ou
  grava um `Lancamento` por parcela — `id` novo, o `pacienteId` e o `planoId` do plano, valor e vencimento, sem
  `pagoEm` nem `forma` — junto com os que já existiam, numa escrita só (`substituirTudo`): entram todos ou nenhum.
  **Lança** se o plano não existe, não está aprovado nem em andamento, ou já tem lançamentos: a tela nem oferece
  esses casos, então é engano de quem chama.
- A soma dos lançamentos gerados é exatamente o total do plano; o resto dos centavos vai para as primeiras parcelas e
  o dia 31 cai no último dia dos meses curtos ([[ParcelamentoDoOrcamento]]).
- O 1º vencimento não precisa ser futuro: a regra só pede um dia que exista, em `AAAA-MM-DD`.

## Histórico de mudanças

- [[2026-09-30-pr-127-financeiro-parcelas]] — `planosParaParcelar`, `calcularParcelas` e `gerarParcelas`.
