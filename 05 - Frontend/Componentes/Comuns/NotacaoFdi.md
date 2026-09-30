---
tipo: funcionalidade
camada: frontend
area: Comuns
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, comuns, odontograma, fdi]
---

# Notação FDI

## O que é

A regra da notação FDI (ISO 3950) do dente: que números existem, em que ordem se desenham e o que cada
número diz — quadrante, dentição, arcada, lado do paciente, tipo e nome. Não tem tela: o odontograma, o
periodontograma e o plano de tratamento a usam. A decisão está no
[[ADR-004-notacao-fdi-no-odontograma]]; os termos, no [[glossario]]. As faces de cada dente, que saem da arcada
e do tipo que esta regra dá, estão em [[FacesDoDente]].

## Onde está no código

- `src/dominio/fdi.ts` — a regra, reexportada por `@/dominio`. As faces por dente moram no mesmo arquivo
  ([[FacesDoDente]]).
- `src/dominio/fdi.test.ts` — os testes.
- `NumeroDente` (só `number`) e `Face` ficam em `src/dominio/odontologia.ts` ([[TiposDoDominio]]).

## Comportamento

- **Listas na ordem de exibição**: `DENTES_PERMANENTES` e `DENTES_DECIDUOS`, cada uma com `superior` e
  `inferior`, da esquerda para a direita de quem olha o paciente. Permanentes: `18→11 | 21→28` em cima e
  `48→41 | 31→38` embaixo. Decíduos: `55→51 | 61→65` em cima e `85→81 | 71→75` embaixo. A linha média é o
  meio de cada lista. São 32 permanentes e 20 decíduos.
- **Os números não são contínuos** (depois do `18` vem o `21`): iterar e ordenar é por essas listas, nunca
  por `n + 1`.
- **`denteValido(n)`** diz se o número é de um dente que existe (o `19` e o `56` não). Aceita qualquer valor
  e devolve `boolean`: `"11"`, `null` e `11.5` dão `false`.
- **`quadrante(n)`** é o primeiro dígito, de 1 a 8. **`ehDeciduo(n)`** é verdadeiro nos quadrantes 5 a 8.
- **`arcada(n)`**: superior nos quadrantes 1, 2, 5 e 6; inferior nos 3, 4, 7 e 8.
- **`lado(n)`**, sempre do paciente: direito nos quadrantes 1, 4, 5 e 8; esquerdo nos 2, 3, 6 e 7.
- **`tipoDente(n)`** pela posição (o segundo dígito):

  | Posição | Permanente | Decíduo |
  |---|---|---|
  | 1 | incisivo central | incisivo central |
  | 2 | incisivo lateral | incisivo lateral |
  | 3 | canino | canino |
  | 4 | pré-molar (primeiro) | molar (primeiro) |
  | 5 | pré-molar (segundo) | molar (segundo) |
  | 6 a 8 | molar (primeiro a terceiro) | — |

  O decíduo não tem pré-molar. `TIPOS_DENTE` traz o rótulo de cada tipo (chave sem acento).
- **`nomeDente(n)`**: tipo, dentição e posição por extenso — `primeiro molar superior direito` (16),
  `segundo molar decíduo inferior esquerdo` (75). Só o pré-molar e o molar levam ordinal, contado a
  partir da linha média. Os 52 nomes são diferentes entre si.
- **Dente que não existe** lança `RangeError` em `quadrante` e em tudo que deriva dele, em vez de
  adivinhar. Para conferir dado de fora sem exceção, use `denteValido`.

## Histórico de mudanças

- [[2026-09-30-pr-053-odontograma-notacao-fdi]] — a regra da notação: listas, validade, quadrante, dentição, arcada, lado, tipo e nome.
- [[2026-09-30-pr-065-odontograma-faces-por-dente]] — as faces de cada dente entram no mesmo arquivo, com nota própria: [[FacesDoDente]].
- [[2026-09-30-pr-097-tratamentos-itens]] — o formulário do item do plano escolhe o dente por `nomeDente` ([[Plano de tratamento]]).
