---
tipo: funcionalidade
camada: frontend
area: Comuns
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, comuns, odontograma, fdi]
---

# Faces do dente

## O que é

Quais faces cada dente tem e como se nomeiam — a regra que o desenho do dente, o registro por face e o
plano de tratamento usam. Não tem tela. Mora ao lado da [[NotacaoFdi]], porque sai da arcada e do tipo do
dente. A decisão está no [[ADR-004-notacao-fdi-no-odontograma]]; o significado de cada face, no
[[glossario]].

## Onde está no código

- `src/dominio/fdi.ts` — `facesDoDente`, `faceValida` e `nomeFace`, reexportadas por `@/dominio`.
- `src/dominio/fdi.test.ts` — os testes.
- O tipo `Face` (`V`, `L`, `P`, `M`, `D`, `O`, `I`) fica em `src/dominio/odontologia.ts`
  ([[TiposDoDominio]]).

## Comportamento

- **`facesDoDente(n)`** devolve as cinco faces do dente, sempre nesta ordem: `V`, `M`, `D`, a de dentro
  da boca e a de cima.

  | Dente | Dentro da boca | De cima |
  |---|---|---|
  | superior, incisivo ou canino | P | I |
  | superior, pré-molar ou molar | P | O |
  | inferior, incisivo ou canino | L | I |
  | inferior, pré-molar ou molar | L | O |

  A arcada vem de `arcada(n)`. Incisivos e caninos são os da frente (posições 1 a 3). O decíduo entra sem
  caso especial: as posições 4 e 5 são molares, então a face de cima é `O`. Lança `RangeError` se o dente
  não existe.
- **`faceValida(n, face)`**: `true` se a face existe no dente. `V`, `M` e `D` valem em todos; `I` só nos
  da frente, `O` só nos de trás, `P` só nos superiores e `L` só nos inferiores. A face é texto e o dente
  pode ser qualquer número: face ou dente que não existe dá `false`, sem lançar. É a função para conferir
  dado guardado ou digitado.
- **`nomeFace(face)`**: `vestibular`, `mesial`, `distal`, `lingual`, `palatina`, `oclusal` e `incisal`,
  em minúsculas — cabe em frases como "face mesial do dente 16".

## Histórico de mudanças

- [[2026-09-30-pr-065-odontograma-faces-por-dente]] — `facesDoDente`, `faceValida` e `nomeFace`.
- [[2026-09-30-pr-097-tratamentos-itens]] — o formulário do item do plano mostra só as faces do dente escolhido, por `facesDoDente` ([[Plano de tratamento]]).
