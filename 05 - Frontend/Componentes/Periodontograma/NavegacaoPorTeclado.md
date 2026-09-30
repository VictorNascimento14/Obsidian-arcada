---
tipo: funcionalidade
camada: frontend
area: Periodontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, periodontograma, teclado]
---

# Navegação por teclado da grade

## O que é

Como se percorre a [[GradeDeSondagem]] sem o mouse, e a arcada inferior que entrou junto. Um exame tem 32 dentes e
384 campos: com o teclado, quem anota vai medindo e digitando sem tirar a mão dele. É o item 4.3 do
[[2026-09-30-plano-da-v1]].

## Onde está no código

- `src/modulos/periodontograma/AbaPeriodonto.tsx` — `ARCADAS` (as duas grades, a superior primeiro) e `navegar`,
  o `onKeyDown` do contêiner que as reúne.
- `src/modulos/periodontograma/GradeDeSondagem.tsx` — cada campo leva `data-celula`, que é o que `navegar`
  percorre.

## Comportamento

- **Arcada inferior**: a mesma grade, dos dentes 48 a 41 e 31 a 38, com os sítios de dentro chamados de linguais
  (ML, L e DL; `lingual` no nome por extenso de cada campo). A superior segue com MP, P e DP.
- **Tab** segue a ordem do documento: os seis sítios da profundidade, depois os seis da margem, e então o primeiro
  campo do dente seguinte. Não há `tabindex`, e um teste confere a ordem.
- **Seta para a direita e para a esquerda**: o campo seguinte e o anterior da linha, o que é de sítio em sítio. No
  fim da profundidade a direita segue para a margem do mesmo dente, e na ponta da linha vai ao dente vizinho — como o
  Tab.
- **Seta para cima e para baixo**: o mesmo campo do dente de cima e do de baixo, e passa da arcada superior para a
  inferior (do 28 ao 48) e volta. O passo vertical é o número de campos da linha, lido da própria linha.
- **O campo que recebe o foco vem com o conteúdo selecionado**: digitar troca o valor em vez de acrescentar
  (voltar a um campo com `3` e digitar `4` dá `4`).
- **Nas pontas da grade** a seta fica onde está — e continua sendo da grade, para cima e para baixo não somarem ao
  número.
- **Com Shift, Ctrl, Alt ou Meta** a seta é do navegador (estender a seleção, por exemplo); Tab, dígitos e Enter também.

## Movimento e micro-interações

O foco é o anel do campo (`focus:ring`). Nada anima.

## Limites conhecidos

- **Sem Enter para avançar** e sem salto automático depois de um dígito: a profundidade vai até 15, e um salto
  depois do primeiro dígito erraria os valores de dois dígitos.
- **A seta não move o cursor dentro do texto do campo**: os valores têm de um a três caracteres, e apagar e digitar de
  novo é mais rápido que posicionar o cursor.
- **As setas valem só nos campos da grade**; em qualquer outro campo da ficha continuam sendo do navegador.

## Histórico de mudanças

- [[2026-09-30-pr-146-periodontograma-teclado-arcada-inferior]] — a arcada inferior e a navegação por Tab e setas.
