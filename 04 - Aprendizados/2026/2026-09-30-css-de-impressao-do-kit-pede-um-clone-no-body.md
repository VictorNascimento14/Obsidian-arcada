---
tipo: aprendizado
data: 2026-09-30
contexto: PR #131 — impressão da anamnese
tags: [aprendizado, impressao, kit]
---

# O CSS de impressão do kit pede um clone no `<body>`

Origem: [[2026-09-30-pr-131-anamnese-impressao]].

## O sintoma

Chamar `window.print()` numa tela do Arcada não imprime "a tela": o papel sai com o que a página tem — coluna
lateral, cabeçalho, barra de baixo, avisos. E o CSS de impressão do kit está lá, em `src/ui/index.css`, mas
nada muda enquanto ninguém o ativa.

## A causa

As regras `@media print` do kit (bloco "Exportar PDF") não escolhem o que imprimir por classe da tela. Elas
escondem tudo o que é filho direto do `<body>`, menos um elemento marcado com `data-print-clone`, e só quando o
próprio `<body>` tem `data-print-mode="clone"`. Na tela, `[data-print-clone]` fica `display: none`. O helper que
montava esse clone no kit de origem (o `imprimirComoPdf`, citado no comentário do CSS) não veio para o Arcada:
o CSS chegou sem o código que o aciona.

## A regra do Arcada

Quem imprime uma folha: (1) monta a folha por portal, **direto no `<body>`**, com `data-print-clone` (a regra
usa o seletor `body > [data-print-clone]`, então dentro de outro elemento não vale); (2) liga
`document.body.dataset.printMode = "clone"`; (3) chama `window.print()`; (4) desfaz tudo no **`afterprint`**. A
folha só existe durante a impressão. O mecanismo virou peça compartilhada no PR #141 ([[2026-09-30-pr-141-documentos-folha]]): [[FolhaImpressa]] e o hook `useImpressao`, em `src/componentes/` — use-os em vez de copiar o efeito. A anamnese, que veio antes, segue com a implementação própria (`FolhaDaAnamnese.tsx` e o efeito de `HistoricoDeVersoes.tsx`).

Dois cuidados que já custaram tempo: a folha usa **cor fixa** (`text-black`), porque no tema escuro os tokens do
app são claros e sumiriam no papel branco; e o **`afterprint` também vem do `printToPDF` do Chrome**, então quem
confere por protocolo de depuração precisa acionar a impressão de novo antes de cada PDF.

## Como evitar

- O recibo e o orçamento (módulos Financeiro e Tratamentos) ainda vão imprimir: usem [[FolhaImpressa]] e
  `useImpressao`, que o PR #141 extraiu para `src/componentes/` (era o `imprimir(folha)` que esta nota pedia
  para quando o segundo módulo aparecesse), em vez de copiar o efeito.
- Não remover a folha logo depois do `window.print()`: em alguns navegadores (celular) ele devolve antes de a
  impressão ler a página, e só o `afterprint` diz que acabou.
- Para conferir sem impressora: Chrome headless com `--remote-debugging-port`, trocar `window.print` por um
  contador, acionar a impressão pela tela e gerar o PDF com `Page.printToPDF`; depois `pdftotext` (o que saiu) e
  `pdftoppm` (como saiu). O texto da tela inteira no PDF é o sinal de que o clone não foi ativado.
- Veja [[ADR-002-sistema-visual-vidro-organico]] para o que o kit faz no vidro em impressão (sem `backdrop-filter`
  nem sombra).
