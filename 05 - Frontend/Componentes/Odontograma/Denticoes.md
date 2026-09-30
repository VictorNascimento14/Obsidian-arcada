---
tipo: funcionalidade
camada: frontend
area: Odontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, odontograma, denticao]
---

# Dentição permanente, decídua e mista

## O que é

O seletor que faz o `Odontograma` ([[Arcadas]]) alternar entre a dentição permanente, a decídua (dentes de leite) e
a mista, que mostra as duas. Quem escolhe é o profissional: o app não decide qual dentição o paciente tem nem
sugere uma pela idade. Ainda não há rota: o módulo Odontograma não tem `modulo.ts`. É o item 3.6 do
[[2026-09-30-plano-da-v1]]; os números dos dentes de leite (51 a 55, 61 a 65, 71 a 75, 81 a 85) são os da
[[ADR-004-notacao-fdi-no-odontograma]].

## Onde está no código

- `src/modulos/odontograma/Odontograma.tsx` — `Odontograma` guarda a dentição escolhida; o `SeletorDenticao` e a
  `Arcada` moram no mesmo arquivo e não se exportam.
- `src/modulos/odontograma/Odontograma.test.tsx` — os testes, com os das arcadas ([[Arcadas]]).

## Comportamento

- **O seletor** é um grupo com o nome `Dentição` e três opções, `Permanente`, `Decídua` e `Mista`. São `input`
  do tipo `radio` de verdade, estilizados como as pílulas das abas da ficha: as setas do teclado trocam a opção e o
  leitor de tela anuncia o grupo, sem código de teclado. Abre em `Permanente`.
- **O que cada opção mostra**, de cima para baixo:

  | Opção | Arcadas na tela |
  |---|---|
  | Permanente | `Arcada superior` (18 a 11 e 21 a 28) e `Arcada inferior` (48 a 41 e 31 a 38) |
  | Decídua | `Arcada superior decídua` (55 a 51 e 61 a 65) e `Arcada inferior decídua` (85 a 81 e 71 a 75) |
  | Mista | `Arcada superior`, `Arcada superior decídua`, `Arcada inferior decídua` e `Arcada inferior` |

  Na mista as duas arcadas de leite ficam juntas, no meio, junto ao plano de mordida, e as permanentes nas
  pontas. Cada arcada é um grupo com o nome da tabela.
- **A linha média das arcadas de leite** (entre o 51 e o 61 e entre o 81 e o 71) fica alinhada com a das
  permanentes: as metades de uma arcada têm a mesma largura, então as arcadas de 10 dentes ficam centradas nas de
  16 sem cálculo.
- **O tamanho do dente** é o mesmo em todas as arcadas, e a rolagem horizontal do celular é a das arcadas.
- **A escolha vive no componente**: fechar e reabrir a tela volta para `Permanente`. Não é guardada por paciente.

## Movimento e micro-interações

A pílula escolhida troca de cor com `transition-colors`; o foco por teclado desenha o anel em volta da pílula
(não do `input`, que é `sr-only`). Conferido no Chrome: a seta troca a opção e a tela acompanha.

## Limites conhecidos

- **260 paradas de `Tab` na mista** (52 dentes, cinco faces cada) e 160 na permanente: setas entre os dentes
  ficaram como melhoria.
- **A escolha não é guardada**: cada vez que a tela abre, é a permanente. Guardar por paciente é decisão de quem
  registrar a dentição (3.7).

## Histórico de mudanças

- [[2026-09-30-pr-094-odontograma-denticao]] — o seletor de dentição e as arcadas de leite.
