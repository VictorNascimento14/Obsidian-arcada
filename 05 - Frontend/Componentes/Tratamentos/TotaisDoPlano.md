---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, orcamento]
---

# Totais do plano

## O que é

As contas do plano de tratamento: o subtotal, o total do orçamento e os itens já realizados. Não têm
tela — a ficha, a lista de planos, a impressão do orçamento e o progresso do tratamento as usam. O
total não se guarda no plano: calcula-se aqui, sempre do mesmo jeito.

## Onde está no código

- `src/modulos/tratamentos/plano.ts` — `subtotal`, `total` e `itensRealizados`.
- `src/modulos/tratamentos/plano.test.ts` — os testes.
- `src/dominio/tratamento.ts` — `PlanoTratamento` e `ItemPlano`, com o campo `realizadoEm`.

## Comportamento

- **`subtotal(plano)`**: a soma dos preços dos itens, em centavos, antes do desconto. Cada item conta
  uma vez, mesmo com o mesmo procedimento em dentes diferentes. Plano sem itens dá `0`.
- **`total(plano)`**: o subtotal menos o desconto do plano. **Nunca é negativo**: o desconto é um valor
  fixo em centavos, e se um item sair do plano depois ele pode passar do subtotal — o total zera em vez
  de inverter o sinal.
- **`itensRealizados(plano)`**: os itens que têm `realizadoEm` (o dia em que o procedimento foi feito),
  na ordem do plano. O progresso do tratamento é essa lista sobre `plano.itens`.
- **`ItemPlano.realizadoEm`** é opcional: ausente é ainda a fazer, no molde de `Lancamento.pagoEm`.
  Quem grava o dia é o atendimento; estas funções só leem.
- **O percentual do desconto não é guardado** — o plano só tem o valor em centavos. Mudar os itens
  depois não recalcula o desconto. A conta do desconto está em [[DescontoDoOrcamento]].
- Tudo em centavos inteiros (ADR-005): a soma de inteiros fecha, sem o erro de centavo do ponto
  flutuante.

## Histórico de mudanças

- [[2026-09-30-pr-049-tratamentos-plano]] — `subtotal`, `total`, `itensRealizados` e o campo `realizadoEm` do item.
- [[2026-09-30-pr-068-tratamentos-desconto]] — o desconto ganha a própria conta, em [[DescontoDoOrcamento]].
