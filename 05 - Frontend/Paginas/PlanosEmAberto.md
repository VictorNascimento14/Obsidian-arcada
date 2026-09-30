---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: /tratamentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, plano, lista]
---

# Planos em aberto

## O que é

A lista de trabalho do módulo Tratamentos: os planos que ainda não terminaram nem foram recusados, de todos os
pacientes, com quem é o paciente, a situação e o total. Abre pelo item **Tratamentos** da coluna lateral (grupo
Gestão, ícone de recibo), e cada linha leva à tela do plano ([[PlanoDeTratamento]]).

## Onde está no código

- `src/modulos/tratamentos/modulo.ts` — a rota `/tratamentos` e o item da coluna (grupo `gestao`, ordem 10, ícone
  `receipt`).
- `src/modulos/tratamentos/PlanosEmAberto.tsx` — a tela.
- `src/modulos/tratamentos/emAberto.ts` — `SITUACOES_EM_ABERTO` e `planosEmAberto`, a regra de quais planos entram
  e em que ordem.
- Reaproveita `SituacaoBadge.tsx` e `rotuloItens` (`exibicao.ts`), e `total` de `plano.ts` ([[TotaisDoPlano]]).

## Comportamento

- **Em aberto** é proposto, aprovado ou em andamento: as situações que ainda têm um próximo passo
  ([[SituacaoDoPlano]]). Concluído e recusado, o fim do caminho, ficam de fora. O aprovado entra porque, sem ele, um
  plano aprovado que ainda não teve o primeiro procedimento feito sumiria da lista.
- **Ordem**: primeiro os propostos, que esperam a resposta do paciente; depois os aprovados e os em andamento. Em
  cada grupo, pelo nome do paciente (`Ágata` antes de `Ana`: a comparação é em pt-BR) e, no mesmo paciente, pela
  ordem em que os planos foram criados.
- **Cada linha**: avatar com as iniciais, nome do paciente, `N itens`, a pílula da situação e o total do plano, já
  com o desconto. A linha inteira é o link para `/planos/:planoId`.
- **Resumo**: `2 planos, somando R$ 100,00` (ou `1 plano`): a soma dos totais dos planos que estão na lista.
- **Plano de paciente que já não existe**: aparece como `Paciente não encontrado`, sem avatar e à frente do grupo,
  em vez de sumir.
- **Sem plano em aberto**: `Nenhum plano em aberto. Os planos nascem na aba Tratamentos da ficha de cada
  paciente.`, com o botão `Ir para os pacientes`. A lista não cria plano: precisa de um paciente, e quem o escolhe é
  a ficha.
- Na tela do plano (`/planos/:planoId`), nenhum item da coluna fica marcado: a coluna marca pelo prefixo do caminho
  e o plano não mora sob `/tratamentos`.

## Movimento e micro-interações

Cartão de vidro; a linha ganha um fundo suave ao passar o mouse.

## Histórico de mudanças

- [[2026-09-30-pr-103-tratamentos-em-aberto]] — a lista dos planos em aberto e o item Tratamentos na coluna lateral.
