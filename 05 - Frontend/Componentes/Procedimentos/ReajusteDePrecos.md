---
tipo: funcionalidade
camada: frontend
area: Procedimentos
rota: /procedimentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, procedimentos, preco, reajuste]
---

# Reajuste de preços em lote

## O que é

O modal, aberto pela [[ListaDeProcedimentos]], que reajusta por um percentual o preço de tabela dos procedimentos
selecionados. Mostra a prévia — o preço atual e o novo de cada um — antes de gravar.

## Onde está no código

- `src/modulos/procedimentos/ReajusteDePrecos.tsx` — o modal.
- `src/modulos/procedimentos/reajuste.ts` — `lerPercentual`, `reajustarPreco` e `aplicarReajuste`.
- `src/modulos/procedimentos/ListaProcedimentos.tsx` — a seleção (uma caixa por linha e «Selecionar todos») e o
  botão «Reajustar preços».
- Dados: grava na coleção `procedimentos`, em `src/dados/colecoes.ts`.

## Comportamento

- **Seleção**: cada linha tem uma caixa de seleção (clicar no nome também marca). «Selecionar todos» marca a lista
  inteira; com busca ou filtro ativo vira «Selecionar os visíveis» e marca só o que aparece. A contagem da lista
  soma `· N selecionados`. A marca sobrevive a uma mudança de filtro, mas o reajuste só alcança o que está
  **visível e marcado**: o botão «Reajustar preços» fica desabilitado sem nenhum marcado à vista.
- **Percentual**: número com sinal, de -100 a 1000, com até duas casas — `5`, `-10`, `+7,5`, `12.25` (vírgula ou
  ponto). Positivo aumenta, negativo reduz; `-100` zera o preço, mas não o deixa negativo. Vazio, texto solto e o que
  passa dos limites mostram o erro no campo.
- **Prévia**: uma linha por selecionado, com o preço atual e o novo. Não grava nada; «Cancelar», Esc e clique fora
  fecham sem mexer nos preços.
- **Aplicar reajuste**: só habilita com um percentual válido que mude ao menos um preço. Percentual zero, ou um preço
  tão pequeno que o arredondamento o deixa como está, mostra «Nenhum preço muda com este percentual.» Ao aplicar,
  grava tudo de uma vez (ou todos os preços mudam, ou nenhum), avisa «N preços reajustados», fecha o modal e limpa a
  seleção.
- **Arredondamento**: centavo mais próximo, **meio centavo sobe**, uma vez, no preço novo (`reajustarPreco`).
  A conta é em inteiros — [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]] — e o percentual vale
  com duas casas.
- **O que não muda**: só o `preco` da tabela. O item de um plano de tratamento já montado guarda a própria cópia do
  preço (`ItemPlano.preco`); o reajuste vale para os itens novos. Procedimento inativo, quando marcado, também é
  reajustado.

## Movimento e micro-interações

O modal é o do kit: o foco entra nele e volta ao botão que o abriu quando fecha. Ao aplicar, um aviso passageiro
confirma quantos preços mudaram.

## Histórico de mudanças

- [[2026-09-30-pr-121-reajuste-de-precos-em-lote]] — o reajuste de preços em lote: seleção na lista, percentual com prévia e gravação em uma escrita só.
