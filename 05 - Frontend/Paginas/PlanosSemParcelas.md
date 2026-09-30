---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: /financeiro
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, parcela, orcamento, lista]
---

# Planos sem parcelas

## O que é

A primeira tela do módulo Financeiro: os planos de tratamento aprovados (ou já em andamento) que ainda não têm
parcelas, cada um com o botão **Gerar parcelas**. Abre pelo item **Financeiro** da coluna lateral (grupo Gestão,
ícone de moeda), que também está na barra de baixo do celular. É aqui que o orçamento aprovado vira dinheiro a
receber: cada parcela gerada é um lançamento ([[glossario]]: Parcela e Lançamento).

## Onde está no código

- `src/modulos/financeiro/modulo.ts` — a rota `/financeiro`, o item da coluna (grupo `gestao`, ordem 20, ícone
  `coin`, `barraCelular`) e a aba `Financeiro` da ficha (`abaPaciente`, ordem 50).
- `PaginaFinanceiro.tsx` — a página (`PageShell` e o cartão). `PlanosSemParcelas.tsx` — o cartão: as linhas e o modal.
- `FormularioDasParcelas.tsx` — o modal **Gerar parcelas**. `AbaFinanceiro.tsx` — a aba da ficha.
- As regras estão em [[GeracaoDeParcelas]]. A tela reaproveita `SituacaoBadge`, `rotuloItens` e `total` do módulo de
  tratamentos ([[TotaisDoPlano]]) e `dataBR` do de pacientes; nenhum arquivo de outro módulo foi editado
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).

## Comportamento

- **Quem entra na lista**: plano aprovado ou em andamento sem nenhum lançamento. Proposto (os itens e o desconto
  ainda mudam), concluído e recusado ficam de fora; o plano que ganha as parcelas sai da lista na hora
  ([[SituacaoDoPlano]]).
- **Ordem**: pelo nome do paciente e, no mesmo paciente, pela ordem em que os planos foram criados.
- **Cada linha**: avatar, nome do paciente (link para o plano, `/planos/:planoId`), `N itens`, a pílula da situação,
  o total do plano — já com o desconto — e o botão **Gerar parcelas**. Plano de paciente que já não existe aparece
  como `Paciente não encontrado`, em vez de sumir.
- **O modal** mostra o total do plano e pede o **número de parcelas** (padrão 1) e o **1º vencimento** (padrão: hoje).
  Abaixo, a prévia: cada parcela com a ordem, o vencimento e o valor. Ela acompanha os campos e some enquanto eles
  estão inválidos. **Gerar parcelas** grava; **Cancelar**, o Escape e o clique fora fecham sem gravar.
- **Erros no campo**: número fora de 1 a 60 (`Informe de 1 a 60 parcelas.`), parcela de menos de R$ 0,01
  (`Cada parcela precisa ter ao menos R$ 0,01.`), vencimento vazio ou que não existe
  (`Informe o dia do primeiro vencimento.`).
- **Sem plano a parcelar**: `Nenhum plano aguardando parcelas. Aprove um plano em Tratamentos e ele aparece aqui.`,
  com o link para `/tratamentos` ([[PlanosEmAberto]]).
- **Sem volta na v1**: não há como apagar lançamentos (o estorno de baixa, item 10.8, desfaz só o pagamento). Por
  isso a prévia antes de gerar, e por isso o plano já parcelado não é oferecido de novo.

## A aba Financeiro da ficha

Os lançamentos do paciente, do vencimento mais antigo ao mais novo, cada um com `Vence em 15/10/2026`, `Em aberto`
(ou `Pago em …`) e o valor. Sem lançamento, diz onde as parcelas nascem e leva a `/financeiro`. É a aba de ordem 50
da [[FichaDoPaciente]]. A situação da parcela (a vencer, vence hoje, vencida) chega no item 10.2 do plano da v1, e a
baixa, no 10.3.

## Movimento e micro-interações

Cartão de vidro; a parte de nome e itens de cada linha ganha um fundo suave ao passar o mouse. O modal abre com a
animação do kit, o foco entra no painel e volta ao botão que o abriu.

## Histórico de mudanças

- [[2026-09-30-pr-127-financeiro-parcelas]] — a tela `/financeiro` com os planos sem parcelas e o modal **Gerar parcelas**; a aba **Financeiro** da ficha.
