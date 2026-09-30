---
tipo: indice
ultima_atualizacao: 2026-09-30
tags: [indice, design]
---

# Linguagem visual

O Arcada usa o sistema **vidro-orgânico** sem alteração de fundação: verde profundo sobre base quase
branca esverdeada, cartões de vidro fosco, pílulas, coluna lateral flutuante que desdobra.

Decisão: [[ADR-002-sistema-visual-vidro-organico]].

## Vocabulário de movimento (fechado)

- Curva `--ease-organic: cubic-bezier(0.22, 0.68, 0, 1)` — sai rápido, assenta devagar.
- Entradas: `rise`, `fade-up`, `fade-in`, `pop-in`, `bar-grow`, `shimmer`, `live-dot`.
- Micro-interações: `.lift`, `.press`, `.sheen`, `.underline-grow`.
- Abrir é mais lento que fechar. `animation-fill-mode: backwards`, nunca `both`.

As invariantes completas estão no `CLAUDE.md` do repositório de código (seção "Sistema visual").
