---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 89
url: https://github.com/VictorNascimento14/Arcada/pull/89
branch: feat/odontograma-arcadas
tags: [pr, odontograma, arcadas, acessibilidade]
status: merged
---

# PR #89 — feat(odontograma): desenhar as arcadas superior e inferior

## 🎯 Contexto

Item 3.5 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): as duas arcadas permanentes e a linha média, sobre o `Dente` do item 3.4. A ordem dos dentes vem de `DENTES_PERMANENTES` em `src/dominio/fdi.ts` ([[ADR-004-notacao-fdi-no-odontograma]]). Fecha a issue #86.

## 🔧 Mudanças

- `src/modulos/odontograma/Odontograma.tsx` — `Odontograma` (duas arcadas) e a `Arcada` interna: metade direita do paciente, linha média e metade esquerda.
- `src/modulos/odontograma/Odontograma.test.tsx` — nome das arcadas, ordem da FDI, cinco faces por dente e a linha média.
- [[Arcadas]] — nota nova de componente no cofre.

## 🧠 Decisões técnicas

- **Cada arcada é duas metades de mesma largura com a linha média no meio.** A linha cai sempre no centro da arcada, então as arcadas decíduas (10 dentes, item 3.6) vão se alinhar com as permanentes (16) sem cálculo.
- **A linha média é um `role="separator"` com nome, entre as duas metades no DOM**, não uma borda: assim ela está na ordem de leitura (entre o 11 e o 21) e o teste confere a posição, coisa que classe de CSS não deixa.
- **Tamanho de dente fixo, arcada com rolagem** (`--dente`: 2,75rem e 3,5rem a partir do `md`), em vez de dentes fluidos: 16 colunas encolhidas num celular deixariam cada face pequena demais para o toque. O contêiner é `w-max` com `mx-auto`: cabendo, centraliza; não cabendo, as margens automáticas viram zero e a rolagem começa no 18 (um `justify-center` cortaria o começo).
- **A variável é escrita por extenso nas classes** (`[--dente:2.75rem]`, `md:[--dente:3.5rem]`, `w-[var(--dente)]`): nada é montado em runtime, então o Tailwind gera as três.
- **Sem props e sem `Legenda` dentro.** O `Odontograma` só desenha as arcadas; quem o usa (a aba, no 3.7) decide o que fica ao lado.

## ⚠️ Armadilhas e aprendizados

- **`overflow-x-auto` corta o anel de foco** que passa da borda do contêiner (o `outline` do `:focus-visible` tem 2 px mais 2 px de distância): por isso o contêiner de rolagem tem `p-2`.
- **`justify-center` num contêiner que rola corta o começo do conteúdo** (não dá para rolar até lá); `w-max` com `mx-auto` não tem esse problema.
- **160 paradas de `Tab`** nas duas arcadas (cinco faces por dente): navegar por setas, com um `Tab` por arcada, ficou de fora.
- **Faces pequenas no toque**: a central mede cerca de 18 px com o dente a 44 px, e os trapézios, 11 px. O odontograma é um mapa, com a disposição como informação, mas a área de toque merece conferência num tablet real quando a aba existir (3.7).
- **Conferência visual feita em Chrome headless, numa página temporária que não entrou no PR**: com 1280 px as 16 colunas cabem (sem rolagem) e, com 390 px, a arcada rola dentro do cartão com a página sem rolagem (390 de 390). A linha média fica entre o 11 e o 21 e entre o 41 e o 31 nos dois casos.

## 🧪 Como testar

1. `npx vitest run src/modulos/odontograma` passa: as duas arcadas com nome, os dentes na ordem de `DENTES_PERMANENTES` (18 a 11 e 21 a 28; 48 a 41 e 31 a 38), 16 dentes com cinco faces em cada arcada e a linha média entre o 11 e o 21 e entre o 41 e o 31.
2. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam, e as classes do layout (`w-[var(--dente)]`, `[--dente:2.75rem]`, `md:[--dente:3.5rem]`, `w-max`) estão no CSS do build.
3. Ver a tela: monte `<Odontograma />` numa página temporária (a aba da ficha vem no 3.7). Com 1280 px de largura as 16 colunas cabem, sem rolagem; com 390 px a arcada rola na horizontal dentro do cartão e a página não rola. Nos dois casos a linha média fica entre o 11 e o 21 e entre o 41 e o 31.

## 📎 Documentação afetada

- [[Arcadas]]
- [[Dente]]
- [[ADR-004-notacao-fdi-no-odontograma]]
- [[2026]] (changelog)
