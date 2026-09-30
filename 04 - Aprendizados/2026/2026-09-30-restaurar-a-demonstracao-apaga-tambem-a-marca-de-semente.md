---
tipo: aprendizado
data: 2026-09-30
contexto: PR #178 — restaurar os dados de demonstração
tags: [aprendizado, sistema, sementes, dados]
---

# Restaurar a demonstração apaga também a marca de semente

Origem: [[2026-09-30-pr-178-sistema-restaurar]].

## O que a regra diz

- Os dados de demonstração nascem de **semeadores** (`src/dados/sementes.ts`). Cada um roda **uma vez por
  versão** e só preenche coleção **vazia**. O «uma vez» fica registrado numa **marca** por semeador, a chave
  `arcada:sementes:<chave>`, que guarda a versão.
- `carregarSementes` **pula** o semeador cuja marca já está na versão do código.
- Por isso restaurar a demonstração apaga as coleções **e as marcas**. Com a marca no lugar e a coleção vazia, a
  página recarrega vazia: nenhum semeador roda de novo.
- Só as chaves `arcada:*` saem. O tema e a coluna recolhida (`arcada-tema`, `arcada-sidebar-collapsed`) usam traço
  em vez de dois-pontos e ficam fora do prefixo: a restauração nem os enxerga.

## Como evitar o erro

- Quem escreve «limpar os dados» como «apagar as coleções» esquece a marca. O teste cobre: depois de
  `restaurarDemonstracao`, `carregarSementes` tem de chamar o semeador de novo.
- A semeadura vem **depois de recarregar**, não antes: as coleções guardam o estado em memória, e um semeador só
  planta em coleção vazia.
