---
tipo: aprendizado
data: 2026-09-30
contexto: PR #25 — tipos do domínio e dinheiro em centavos
tags: [aprendizado, dinheiro, teste]
---

# O Intl separa o `R$` do número com espaço não separável

Origem: [[2026-09-30-pr-025-tipos-do-dominio]].

## O sintoma

Um teste que compara `formatarReais(300)` com a string `"R$ 3,00"`, escrita à mão, reprova — e a diferença não
aparece no diff: as duas strings parecem iguais na tela e no editor.

## A causa

O `Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" })` escreve um **espaço não separável**
(U+00A0) entre o `R$` e o número, não o espaço comum (U+0020). É outro caractere, com a mesma aparência.

## A correção

- No teste do formato, escrever o U+00A0 explícito (`"R$\u00a03,00"`), como `dinheiro.test.ts` faz, e conferir o
  código do caractere (`charCodeAt(2)` é `0xa0`).
- Na leitura, `paraCentavos` aceita os dois espaços — o `\s` da expressão regular cobre o U+00A0 —, então o que
  `formatarReais` escreve volta ao mesmo número (o teste confere).

## Como evitar

- Em teste de valor exibido, não digite o texto do real: compare com o retorno de `formatarReais`.
- Em teste de tela, `getByText("R$ 3,00")` com espaço comum **casa**, porque o normalizador da Testing Library trata
  o U+00A0 como espaço; já `expect(elemento.textContent).toBe("R$ 3,00")` **falha**, porque o texto cru guarda o
  U+00A0. Não cole um `R$` copiado da tela para dentro do código sem conferir o caractere.
- Veja [[Dinheiro]] para o comportamento das três funções.
