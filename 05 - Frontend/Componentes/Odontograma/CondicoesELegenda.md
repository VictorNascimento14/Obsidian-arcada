---
tipo: funcionalidade
camada: frontend
area: Odontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, odontograma, condicoes]
---

# Condições e legenda

## O que é

As nove condições que o odontograma registra e a legenda que mostra a cor de cada uma. As condições só
nomeiam o que o profissional registrou: o app não sugere conduta clínica. O significado de cada uma está no
[[glossario]] (seção Condição). Não tem rota própria: as condições se marcam na aba **Odontograma** da ficha do paciente ([[MarcarCondicoes]], item 3.7 do [[2026-09-30-plano-da-v1]]), onde o símbolo de cada uma aparece no dente e na barra de escolha. A `Legenda` também mostra cor e símbolo, mas nenhuma tela a monta ainda.

## Onde está no código

- `src/modulos/odontograma/condicoes.ts` — `CONDICOES` e os tipos `Condicao`, `EscopoCondicao` e
  `CondicaoId`.
- `src/modulos/odontograma/condicoes.test.ts` — os testes da lista.
- `src/modulos/odontograma/Legenda.tsx` — o componente (exportação padrão): desenha o símbolo (`IconeDaCondicao`, de `desenho.tsx`) e o nome de cada condição.
- `src/modulos/odontograma/Legenda.test.tsx` — os testes da legenda.

## Comportamento

- **Lista fechada.** Cada condição tem `id` estável (é o que se guarda no dado, sem acento), `rotulo`,
  `escopo` e `cor`:

  | id | Rótulo | Vale em | Cor (tema claro / escuro) |
  |---|---|---|---|
  | `carie` | Cárie | face | vermelho (rampa `red` do kit) |
  | `restauracao` | Restauração | face | azul 600 / 400 |
  | `selante` | Selante | face | ciano 600 / 400 |
  | `fratura` | Fratura | dente | âmbar 600 / 400 |
  | `extracaoIndicada` | Extração indicada | dente | fúcsia 600 / 400 |
  | `ausente` | Ausente | dente | cinza (rampa `foreground-500` do kit) |
  | `tratamentoDeCanal` | Tratamento de canal | dente | violeta 600 / 400 |
  | `coroa` | Coroa | dente | lima 600 / 400 |
  | `implante` | Implante | dente | esmeralda 700 / 400 |

- **Escopo.** `face` (cárie, restauração e selante) se marca numa face do dente; `dente` (as outras seis)
  vale no dente inteiro.
- **A cor é uma classe `text-*` literal por condição**, com a variante `dark:` fora as rampas do kit,
  escrita por extenso porque o Tailwind só gera a classe que está no fonte. Quem usa a cor a converte pelo
  `currentColor`: `bg-current` no marcador da legenda, `fill-current` ou `stroke-current` num desenho.
- **`Legenda`** mostra as condições em dois grupos, "Por face" e "Dente inteiro", cada um uma lista com nome
  acessível. O símbolo da condição, na cor dela, é decorativo (`aria-hidden`) e o nome está sempre escrito ao lado. Não
  recebe props.

## Limites conhecidos

- A cor não é o único sinal: quatro das nove (vermelho, âmbar, lima e esmeralda) ficam no eixo vermelho–verde
  que o daltonismo mais confunde, e por isso cada condição tem também um símbolo só seu, definido em
  [[MarcarCondicoes]]; o `aria-label` da face também traz a condição em texto.
- O contraste das cores sobre o vidro claro e escuro não foi medido em tela: a `Legenda` ainda não tem tela que
  a monte. Confira na primeira que a usar.

## Histórico de mudanças

- [[2026-09-30-pr-070-odontograma-condicoes-e-legenda]] — a lista de condições e a `Legenda`.
- [[2026-09-30-pr-112-odontograma-marcar]] — o símbolo de cada condição (no dente, na barra e na `Legenda`), definido em [[MarcarCondicoes]].
