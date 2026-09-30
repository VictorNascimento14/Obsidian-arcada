---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 94
url: https://github.com/VictorNascimento14/Arcada/pull/94
branch: feat/odontograma-denticao
tags: [pr, odontograma, denticao, acessibilidade]
status: merged
---

# PR #94 — feat(odontograma): alternar a dentição permanente, decídua e mista

## 🎯 Contexto

Item 3.6 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): alternar permanente, decídua e mista sobre as arcadas do item 3.5 ([[Arcadas]]). Os dentes de leite e a ordem em que se desenham vêm de `DENTES_DECIDUOS` em `src/dominio/fdi.ts` ([[ADR-004-notacao-fdi-no-odontograma]]). Fecha a issue #91.

## 🔧 Mudanças

- `src/modulos/odontograma/Odontograma.tsx` — o estado da dentição, o `SeletorDenticao` (três `radio` nativos) e a lista `ARCADAS` que diz, de cima para baixo, o que cada dentição mostra; a `Arcada` passou a servir aos 10 dentes de leite sem mudar.
- `src/modulos/odontograma/Odontograma.test.tsx` — os testes de dentição (seletor, decídua, mista e a volta à permanente), com o dos das arcadas.
- [[Denticoes]] — nota nova de componente no cofre.

## 🧠 Decisões técnicas

- **Radios nativos, não `role="tablist"`.** Setas, grupo e anúncio do leitor de tela vêm do navegador, e não há painéis para controlar: a escolha só muda o que o mesmo odontograma mostra. As abas da ficha precisam de código de teclado e de `aria-controls`; aqui seria cerimônia.
- **A `Arcada` do item 3.5 não mudou.** As duas metades de mesma largura já centravam qualquer número de dentes: as arcadas de 10 dentes entraram alinhadas na linha média das de 16 sem cálculo nem prop nova.
- **Uma lista `ARCADAS` diz a ordem** (`superior`: permanente e decídua; `inferior`: decídua e permanente), e a dentição escolhida só filtra. Na mista, as duas de leite ficam juntas no meio, junto ao plano de mordida, e as permanentes nas pontas; a ordem não é repetida em `if`.
- **Nome da arcada**: `Arcada superior` para a permanente (é a que abre e o nome que o item 3.5 já dava) e `Arcada superior decídua` para a de leite. Os nomes de grupo são exatos, então um não colide com o outro.
- **A escolha vive no componente** (`useState`, abre em `Permanente`). Guardar a dentição por paciente é decisão do registro (3.7), e o app não sugere dentição pela idade: quem escolhe é o profissional.

## ⚠️ Armadilhas e aprendizados

- **O anel de foco vai na pílula, não no `input`.** O `input` é `sr-only` (1 px); o `:focus-visible` global desenharia o anel em volta dele. Por isso a pílula usa `peer-focus-visible:outline-primary-600`, sobre um `outline` transparente.
- **O `label` é `relative`**: um `input` `sr-only` é `position: absolute` e, sem um ancestral posicionado, o navegador pode rolar a página até onde ele está ao ganhar foco.
- **O `name` do grupo de `radio` sai de `useId`**: duas instâncias do odontograma na mesma tela não dividem o grupo (marcar uma desmarcaria a outra).
- **Captura de tela logo após o clique mostra a transição, não o estado final**: com `transition-colors`, a pílula antiga ainda está escura no quadro seguinte ao clique. Espere ~500 ms antes de conferir a cor.
- **260 paradas de `Tab` na mista** (52 dentes, cinco faces): setas entre os dentes, com um `Tab` por arcada, ficaram de fora.
- **Conferência visual feita em Chrome headless, numa página temporária que não entrou no PR**: decídua e mista a 1280 px com as linhas médias alinhadas, a seta do teclado trocando a opção com o anel de foco na pílula, o tema escuro e a mista a 390 px, com a rolagem dentro do cartão e a página sem rolar (390 de 390).

## 🧪 Como testar

1. `npx vitest run src/modulos/odontograma` passa: o seletor com as três opções e a permanente marcada; na decídua, só as duas arcadas de leite com 10 dentes na ordem da FDI e a linha média entre o 51 e o 61 e entre o 81 e o 71; na mista, as quatro arcadas na ordem cima para baixo; e a volta para a permanente.
2. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam, e as classes do seletor (`peer-checked:bg-primary-900`, `peer-checked:shadow-nav-active`, `peer-focus-visible:outline-primary-600`) estão no CSS do build.
3. Ver a tela: monte `<Odontograma />` numa página temporária (a aba da ficha vem no 3.7). Escolha Decídua e depois Mista: as arcadas de leite ficam centradas na linha média das permanentes. `Tab` leva ao seletor e as setas trocam a opção, com o anel de foco na pílula. Com 390 px de largura a mista rola dentro do cartão e a página não rola.

## 📎 Documentação afetada

- [[Denticoes]]
- [[Arcadas]]
- [[ADR-004-notacao-fdi-no-odontograma]]
- [[2026]] (changelog)
