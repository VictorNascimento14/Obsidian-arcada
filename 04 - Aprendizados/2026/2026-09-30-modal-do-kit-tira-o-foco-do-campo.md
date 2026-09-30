---
tipo: aprendizado
data: 2026-09-30
contexto: PR #186 — busca global de pacientes
tags: [aprendizado, sistema, modal, foco, react]
---

# O Modal do kit tira o foco do campo que está dentro dele

Origem: [[2026-09-30-pr-186-sistema-busca]].

## O que acontece

O `Modal` (`src/ui/base/Modal.tsx`) leva o foco ao botão **Fechar** num `useEffect` ao abrir. Um `autoFocus` no
campo de dentro do `Modal` não adianta: conferido, o teste do foco falha. O campo recebe o foco na montagem, e o
efeito do `Modal`, que roda depois, o puxa para o Fechar. Um `useEffect` em um filho do `Modal` teria o mesmo
destino, porque o efeito de um filho roda antes do efeito do pai.

## Como evitar o erro

- Peça o foco num efeito do componente que **monta** o `Modal` (o pai): ele roda depois do efeito do `Modal`, e o
  portal já está no DOM, então o `ref` do campo já existe. É o que faz a `CaixaDeBusca`
  (`src/modulos/sistema/BuscaGlobal.tsx`).
- Confira em teste: `expect(document.activeElement).toBe(campo)` logo depois de abrir.
