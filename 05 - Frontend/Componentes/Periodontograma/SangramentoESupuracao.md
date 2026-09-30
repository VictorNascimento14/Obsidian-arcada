---
tipo: funcionalidade
camada: frontend
area: Periodontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, periodontograma, sangramento]
---

# Sangramento e supuração por sítio

## O que é

As duas marcas de sim ou não de cada sítio da [[GradeDeSondagem]]: **sangramento à sondagem** e **supuração**. É o
item 4.4 do [[2026-09-30-plano-da-v1]]. Os termos estão no [[glossario]] (Sangramento à sondagem, Supuração): anota-se
por sítio, houve ou não.

## Onde está no código

- `src/modulos/periodontograma/GradeDeSondagem.tsx` — as colunas `Sangramento` e `Supuração` (`SINAIS`), uma marca
  por sítio, e o `aoAlternar`.
- `src/modulos/periodontograma/grade.ts` — `Sinal` e `ROTULOS_DE_SINAL`.
- `src/modulos/periodontograma/dados.ts` — `alternarSinal`.
- `src/modulos/periodontograma/exame.ts` — `MedidaSitio.supuracao`, campo novo e opcional.

## Comportamento

- **Colunas**: depois da margem, dois grupos de seis colunas, um por sítio: **Sangramento** e **Supuração**, com os
  mesmos rótulos de sítio da profundidade (MV, V, DV, MP, P, DP na arcada superior; ML, L, DL na inferior).
- **Ligar e desligar**: um clique na marca, ou Enter e Espaço com o foco nela. Cada marca vale por si: ligar o
  sangramento de um sítio não mexe na supuração dele nem no sangramento do sítio vizinho.
- **Aparência**: desligada é um círculo vazio; ligada, cheio — vermelho no sangramento, laranja na supuração. O
  estado não depende só da cor: cheio ou vazio, e `aria-pressed` diz ao leitor de tela se está ligada.
- **Nome para leitor de tela**: `Sangramento, dente 16, mesiovestibular`, `Supuração, dente 36, distolingual`.
- **Gravação**: ligar guarda `true` no sítio do exame de hoje; desligar tira o campo, porque desmarcado e sem sinal
  são a mesma coisa. As marcas convivem com a profundidade e a margem do mesmo sítio, e voltam ao reabrir a aba.
- **Índices**: o sangramento alimenta o percentual de sangramento do modelo (`indicesDoExame`), que a tela mostra no
  item 4.6; a supuração não entra em índice nenhum.
- **Teclado**: as marcas fazem parte da grade (`data-celula`). A seta para a direita passa da margem ao sangramento
  e deste à supuração; para cima e para baixo vão à mesma marca do dente vizinho, como nos campos. Ver
  [[NavegacaoPorTeclado]].

## Movimento e micro-interações

A cor da marca muda com a transição do kit (`transition-colors`); o foco por teclado é o anel da marca
(`focus-visible:ring`). Nada mais anima.

## Limites conhecidos

- **Sangramento num sítio sem profundidade não conta**: o modelo deixa de fora o sítio ainda não medido, para o
  percentual nunca passar de 100. A marca fica ligada na tela, e o número só a contará quando a profundidade existir.
- **Não há "marcar todos os sítios" nem "limpar o dente"**: cada sítio é uma marca.
- **A marca tem 24 px**, o mínimo aceito para alvo de toque; é o preço de manter a grade em cerca de 890 px, sem
  rolagem em uma ficha aberta em 1440 px.

## Histórico de mudanças

- [[2026-09-30-pr-156-periodontograma-sangramento-supuracao]] — as marcas de sangramento e de supuração em cada sítio.
