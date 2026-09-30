---
tipo: funcionalidade
camada: frontend
area: Odontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, odontograma, arcadas]
---

# Arcadas superior e inferior

## O que é

O componente `Odontograma` na sua primeira forma: as duas arcadas com os 16 dentes permanentes de cada uma, cada
dente desenhado pelo [[Dente]], e a linha média no meio. É o corpo do odontograma que o registro de condições
(item 3.7 do [[2026-09-30-plano-da-v1]]) vai usar; a dentição decídua e a mista se escolhem no seletor de
[[Denticoes]] (item 3.6). Ainda não há rota:
o módulo Odontograma não tem `modulo.ts`, e o `Odontograma` só aparece quando uma tela o usar. A notação (quais
números existem e em que ordem se desenham) é a da [[ADR-004-notacao-fdi-no-odontograma]].

## Onde está no código

- `src/modulos/odontograma/Odontograma.tsx` — o componente (exportação padrão, sem props) e, no mesmo arquivo, a
  `Arcada` (uma linha de dentes), que não se exporta.
- `src/modulos/odontograma/Odontograma.test.tsx` — os testes.

## Comportamento

- **Duas arcadas**, cada uma um `role="group"` com nome: `Arcada superior` (18 a 11 e 21 a 28) e
  `Arcada inferior` (48 a 41 e 31 a 38). A ordem é a de `DENTES_PERMANENTES` em `src/dominio/fdi.ts`: a de quem
  olha o paciente de frente, com o lado direito dele à esquerda da tela.
- **Linha média**: entre o 11 e o 21 em cima e entre o 41 e o 31 embaixo. Cada arcada é a metade do lado direito
  do paciente, a linha e a metade do esquerdo; as duas metades têm a mesma largura, então a linha cai sempre no
  meio da arcada, qualquer que seja o número de dentes. A linha é um `role="separator"` (vertical, nome
  `Linha média`), na ordem do DOM entre o 11 e o 21: quem usa leitor de tela a ouve ao cruzá-la.
- **Tamanho do dente fixo**, dado pela variável CSS `--dente`: 2,75rem (44 px) e, a partir do `md`, 3,5rem
  (56 px). Onde a tela não comporta as 16 colunas, a arcada rola na horizontal dentro do próprio cartão (a
  página não rola). No computador as 16 colunas cabem sem rolagem.
- Cada dente é o [[Dente]]: número acima e cinco faces focáveis. Sem ação por ora: `Odontograma` não recebe
  `onFace`.

## Movimento e micro-interações

Nenhum além do realce de cada face ao passar o mouse, que é do [[Dente]].

## Limites conhecidos

- **160 paradas de `Tab`** nas duas arcadas (5 faces por dente). Um único `Tab` por arcada, com setas entre os
  dentes, ficou como melhoria.
- **Faces pequenas no toque**: a face central mede cerca de 18 px a 44 px de dente (22 px a 56 px) e os trapézios,
  11 px (14 px). O odontograma é um mapa, cuja disposição é a informação, mas a área de toque merece conferência
  num tablet de verdade quando a aba existir (3.7).
- **A rolagem horizontal do celular não tem indicador próprio**: o dente cortado na borda é a pista de que há
  mais à direita.

## Histórico de mudanças

- [[2026-09-30-pr-089-odontograma-arcadas]] — as duas arcadas permanentes com a linha média.
- [[2026-09-30-pr-094-odontograma-denticao]] — o seletor de dentição passa a alternar o que as arcadas mostram: [[Denticoes]].
