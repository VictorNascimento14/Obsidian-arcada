---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 80
url: https://github.com/VictorNascimento14/Arcada/pull/80
branch: feat/odontograma-dente
tags: [pr, odontograma, dente, acessibilidade]
status: merged
---

# PR #80 — feat(odontograma): desenhar o dente com as cinco faces

## 🎯 Contexto

Item 3.4 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): o desenho do dente que as arcadas (3.5) repetem e onde o registro por face e por dente (3.7) vai marcar as condições. A notação e as faces vêm de `src/dominio/fdi.ts` ([[ADR-004-notacao-fdi-no-odontograma]]), já na `main`. Fecha a issue #75.

## 🔧 Mudanças

- `src/modulos/odontograma/Dente.tsx` — o dente em SVG: cinco faces como `<g role="button">` focáveis, com o número acima.
- `src/modulos/odontograma/disposicao.ts` — `disposicaoDasFaces(n)` e `POSICOES`: qual face cai em qual lugar do desenho.
- `src/modulos/odontograma/disposicao.test.ts` — a regra de posição nos oito quadrantes e a permutação das cinco faces em todos os dentes.
- `src/modulos/odontograma/Dente.test.tsx` — nome acessível, foco, clique, Enter, Espaço e a tecla mantida.
- [[Dente]] — nota nova de componente no cofre.

## 🧠 Decisões técnicas

- **A posição das faces é uma função pura à parte (`disposicao.ts`), não lógica do SVG.** É a regra clínica do item (a mesial aponta para a linha média) e, sem layout no jsdom, só assim ela é testável; o `Dente` só desenha o que a função devolve. Também é o que o `react-refresh` do lint exige: arquivo `.tsx` só exporta componente.
- **A regra reusa `fdi.ts`.** `facesDoDente` já devolve as faces em ordem garantida (V, M, D, a de dentro, a de cima) e `lado`/`arcada` dão o lado e a arcada; nada de número de dente ou tabela nova aqui.
- **Cada face é um `<g role="button" tabIndex={0}>` com `aria-label`**, não uma sobreposição de botões HTML: o SVG não perde a escala e o foco desenha o anel em volta da face. O nome é `face <nome> do dente <número>`, o do grupo é `Dente <número>, <nome por extenso>`.
- **Enter e Espaço chamam a mesma ação do clique; o Espaço não rola a página e a tecla mantida (`repeat`) é ignorada.** Um botão nativo repetiria o Enter; aqui a ação vai virar marcar/desmarcar uma condição, e repetir alternaria a marca sem parar.
- **`onFace` é opcional**: as arcadas (3.5) desenham o dente antes de o registro (3.7) existir. O cursor de mão só aparece com `onFace`.
- **O contêiner dá o tamanho**: o `Dente` é `w-full` com o desenho `aspect-square`, para a arcada escolher a largura por breakpoint sem prop de tamanho.
- **Cores por classes literais do kit** (`fill-foreground-950/[0.04]`, `stroke-foreground-500`, `group-hover:fill-primary-500/25`), que trocam sozinhas no tema escuro; as classes de condição (`text-*` de `condicoes.ts`) entram no 3.7 como `fill-current`/`stroke-current`.

## ⚠️ Armadilhas e aprendizados

- **`focus:ring-*` não funciona em SVG** (é `box-shadow`, e o SVG não o desenha), e `focus:outline-none` apagaria o foco. O anel é o `outline` do `:focus-visible` global do kit, que o Chrome desenha em volta do `<g>`: não ponha `outline-none` na face.
- **Sem `<title>` nas faces.** Com `aria-label`, o `<title>` filho vira a descrição acessível e o leitor de tela leria o nome da face duas vezes.
- **A mesial segue o lado do paciente, não o de quem olha.** O 16 é do lado direito do paciente e aparece à esquerda da tela; a linha média fica à direita dele, então a mesial do 16 é a face da direita. A regra vem de `lado(n)`.
- **O desenho é simétrico**: olhando, não dá para ver se a mesial está do lado certo. Quem garante é `disposicao.test.ts` (as posições de 16, 26, 36, 46, 55, 65, 75 e 85, escritas à mão a partir da regra).
- **Cinco paradas de `Tab` por dente** (o pedido é toda face focável): as duas arcadas de um adulto (32 dentes) são 160. Navegar por setas, com um `Tab` por arcada, ficou de fora.
- **Conferência visual feita em Chrome headless, numa página temporária que não entrou no PR**: as cinco faces, o número acima, o tema claro e o escuro e o anel de foco por `Tab`. O toque real num celular não foi conferido: a face central mede cerca de 40% da largura do desenho, e o tamanho final é decisão da arcada (3.5).

## 🧪 Como testar

1. `npx vitest run src/modulos/odontograma` passa: a regra de posição nos oito quadrantes (mesial à direita em 1/4/5/8 e à esquerda em 2/3/6/7), as cinco faces sem repetir em todos os 52 dentes, e o componente (nome acessível, `tabindex`, clique, Enter, Espaço sem rolar a página, tecla mantida ignorada).
2. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam, e as classes do desenho (`fill-foreground-950/[0.04]`, `stroke-foreground-500`, `group-hover:fill-primary-500/25`, `aspect-square`) estão no CSS do build.
3. Ver o desenho: monte `<Dente numero={16} onFace={console.log} />` numa página temporária (a rota vem com as arcadas, 3.5). São cinco faces; o `Tab` passa por elas em ordem de leitura e mostra o anel de foco; clicar, `Enter` ou `Espaço` numa face escreve a letra dela.

## 📎 Documentação afetada

- [[Dente]]
- [[ADR-004-notacao-fdi-no-odontograma]]
- [[glossario]]
- [[2026]] (changelog)
