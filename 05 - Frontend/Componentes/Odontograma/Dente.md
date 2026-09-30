---
tipo: funcionalidade
camada: frontend
area: Odontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, odontograma, dente]
---

# Dente (desenho com cinco faces)

## O que é

Um dente desenhado em SVG, com o número FDI acima e as cinco faces que ele tem, cada uma clicável e focável por
teclado. É a peça que as arcadas repetem (item 3.5 do [[2026-09-30-plano-da-v1]]) e onde o registro de condições
(3.7) vai marcar cada face. Ainda não há rota: o módulo Odontograma não tem `modulo.ts`, e o `Dente` só aparece
quando uma tela o usar. A notação (quais números existem, quais faces cada dente tem) é a da
[[ADR-004-notacao-fdi-no-odontograma]]; o significado de cada face está no [[glossario]].

## Onde está no código

- `src/modulos/odontograma/Dente.tsx` — o componente (exportação padrão): `<Dente numero={16} onFace={...} />`.
- `src/modulos/odontograma/disposicao.ts` — `disposicaoDasFaces(n)`: qual face cai em qual lugar do desenho
  (`cima`, `esquerda`, `centro`, `direita`, `baixo`). Regra pura, sem React.
- `src/modulos/odontograma/disposicao.test.ts` e `Dente.test.tsx` — os testes.

## Comportamento

- **O desenho** é um quadrado com um quadrado menor no centro: o centro é a face de cima do dente e os quatro
  trapézios em volta são as outras quatro.
- **Onde cada face fica** (a regra de `disposicao.ts`, com o dente visto de frente):

  | Lugar | Dente superior | Dente inferior |
  |---|---|---|
  | Centro | I (incisivos e caninos) ou O (pré-molares e molares) | igual |
  | Em cima | V (vestibular) | L (lingual) |
  | Embaixo | P (palatina) | V (vestibular) |
  | Do lado da linha média | M (mesial) | M (mesial) |
  | Do lado oposto | D (distal) | D (distal) |

  A linha média fica à direita da figura para os dentes dos quadrantes 1, 4, 5 e 8 (lado direito do paciente,
  que aparece à esquerda de quem olha) e à esquerda nos quadrantes 2, 3, 6 e 7. Por isso a mesial do 16 fica à
  direita e a do 26, à esquerda. A regra usa `lado(n)` de `src/dominio/fdi.ts`.
- **Cada face é um botão de teclado**: `role="button"`, `tabindex="0"` e nome acessível `face mesial do dente 16`
  (`face <nome> do dente <número>`). Clique, `Enter` e `Espaço` chamam `onFace(face)` com a letra da face (`"M"`).
  O `Espaço` não rola a página e a tecla mantida não repete a ação. Sem `onFace` o dente só desenha.
- **O grupo do desenho** se chama `Dente 16, primeiro molar superior direito`. O número visível acima é
  `aria-hidden`, porque o nome do grupo já o traz.
- **Ordem do `Tab`**: a de leitura (cima, esquerda, centro, direita, baixo).
- **Tamanho**: o `Dente` ocupa a largura do contêiner e o desenho é quadrado (`aspect-square`); quem o usa dá o
  tamanho.
- **Dente que não existe** (19, 56…) lança `RangeError`, como as funções de `fdi.ts`.

## Movimento e micro-interações

Passar o mouse numa face a tinge de leve (`primary-500` a 25%, com `transition-colors`). O foco por teclado é o
anel do `:focus-visible` do kit, desenhado em volta da face. Nada anima além disso.

## Limites conhecidos

- **Cada dente traz cinco paradas de `Tab`** (o pedido é que toda face seja focável): os 32 dentes de um adulto
  são 160 paradas. Setas para andar entre dentes (um único `Tab` por arcada) ficam como melhoria.
- **O desenho é simétrico**: quem garante que a mesial está do lado da linha média é o teste de
  `disposicao.ts`, não o olho. As marcas de condição (3.7) é que tornam o lado visível.
- **A face central mede cerca de 40% da largura do desenho**: a área de toque depende do tamanho que a arcada der
  a cada dente (3.5).

## Histórico de mudanças

- [[2026-09-30-pr-080-odontograma-dente]] — o desenho do dente com as cinco faces, focáveis e com nome acessível.
