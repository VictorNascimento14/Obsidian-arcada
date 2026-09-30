---
tipo: aprendizado
data: 2026-09-30
contexto: PR #147 — baixa de parcela (verificação visual do financeiro)
tags: [aprendizado, teste, ui]
---

# Captura de página inteira não revela o cartão abaixo da dobra

Origem: [[2026-09-30-pr-147-financeiro-baixa]].

## O sintoma

Numa captura com `fullPage: true` (Playwright), no celular, a tela do Financeiro saiu com um vazio no lugar do
segundo cartão. O cartão estava no DOM, com `opacity: 0` e `animation: none`; depois de rolar até ele, a opacidade
voltou a `1`.

## A causa

O `GlassCard` do kit usa `useInView`: só se revela (animação de entrada) quando entra na viewport. A captura de página
inteira não rola a página, então o que está abaixo da dobra não dispara o `useInView` e sai transparente. No desktop
o problema não aparece porque a tela cabe na janela.

## O que fazer

Antes de capturar, ou de medir opacidade, role até cada cartão abaixo da dobra (`scrollIntoViewIfNeeded()` no título
dele) e espere a animação acabar; só então tire a foto. Um vazio numa captura do celular é, primeiro, isto — e não
um cartão que não renderizou.
