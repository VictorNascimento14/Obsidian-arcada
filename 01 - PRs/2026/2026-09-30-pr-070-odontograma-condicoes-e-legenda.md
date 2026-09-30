---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 70
url: https://github.com/VictorNascimento14/Arcada/pull/70
branch: feat/odontograma-condicoes
tags: [pr, odontograma, condicoes]
status: aberto
---

# PR #70 — feat(odontograma): listar as condições do odontograma e a legenda

## 🎯 Contexto

Item 3.3 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): a lista de condições e a legenda que o desenho do dente (3.4) e o registro por face e por dente (3.7) vão usar. Os nomes vêm da tabela de condições do [[glossario]], que deixava as cores e os símbolos como `<A DEFINIR>`; este PR define as cores. O app não sugere conduta clínica: as condições só nomeiam o que o profissional registrou. Fecha a issue #67.

## 🔧 Mudanças

- `src/modulos/odontograma/condicoes.ts` — `CONDICOES` (nove, com `as const satisfies`), `Condicao`, `EscopoCondicao` e `CondicaoId`.
- `src/modulos/odontograma/condicoes.test.ts` — quantidade, unicidade, escopo de cada uma e o formato da cor.
- `src/modulos/odontograma/Legenda.tsx` — a legenda em dois grupos (por face e dente inteiro).
- `src/modulos/odontograma/Legenda.test.tsx` — os grupos e o marcador de cada condição.
- [[CondicoesELegenda]] — nota nova de funcionalidade no cofre.

## 🧠 Decisões técnicas

- **Uma cor por condição, numa classe `text-*` só**: o marcador da legenda a usa como `bg-current`, e o desenho do dente poderá usar `fill-current` ou `stroke-current`. Há uma fonte da verdade por condição, em vez de três classes (fundo, preenchimento, traço) que precisariam concordar. Se um item seguinte precisar de outra classe (fundo suave, por exemplo), acrescenta o campo quando precisar.
- **Classes literais, com a variante `dark:` escrita ao lado.** As rampas `red` e `foreground` são do kit e trocam sozinhas no tema escuro; as demais são da paleta padrão do Tailwind (o config estende, não substitui) e levam `dark:text-*-400`. O teste reprova cor que não seja `text-*` ou que esteja sem a variante escura.
- **`id` em camelCase e sem acento** (`extracaoIndicada`, `tratamentoDeCanal`), como as chaves de `FAIXAS_ETARIAS` e de `TIPOS_DENTE`: é o que vai para o dado guardado, então não muda quando o rótulo mudar.
- **`as const satisfies readonly Condicao[]`** dá o tipo `CondicaoId` (a união dos nove ids) sem repetir a lista; os itens seguintes tipam o dado guardado com ele.
- **A legenda agrupa pelo `escopo`**, o que mostra na própria tela a distinção que o registro (3.7) vai usar: o que se marca numa face e o que vale no dente inteiro.
- **Só o nome, sem descrição.** Os rótulos são os do glossário e a legenda não diz mais nada; o que cada condição significa continua no glossário, e não há texto na tela que possa passar por indicação de conduta.

## ⚠️ Armadilhas e aprendizados

- A cor não pode ser o único sinal: quatro das nove (vermelho, âmbar, lima e esmeralda) ficam no eixo vermelho–verde que o daltonismo mais confunde. A legenda escreve o nome ao lado de cada uma, mas o desenho do dente (3.4 e 3.7) precisa repetir a condição em texto (o `aria-label` da face, por exemplo) ou num símbolo.
- Classe montada em runtime não existe no CSS: por isso `cor` guarda a classe inteira (`text-lime-600 dark:text-lime-400`) e a `Legenda` só a concatena com as outras classes fixas. Ao trocar uma cor, troque o literal em `condicoes.ts`; nada se monta a partir de nome de cor.
- Sem conferência visual: a `Legenda` não está em rota e o navegador de verificação não conectou nesta máquina. A conferência foi o CSS gerado pelo build (todas as classes e as variantes `dark:` estão lá); o contraste real das cores sobre o vidro claro e escuro fica para a primeira tela que usar a legenda (3.5).

## 🧪 Como testar

1. `npx vitest run src/modulos/odontograma` passa: nove condições com id, rótulo e cor únicos; o escopo de cada uma; a cor como classe `text-*` literal, com `dark:` fora as rampas do kit; a legenda em dois grupos, com o marcador decorativo levando as classes da condição.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.
3. Conferir no CSS do build (`out/assets/*.css`) que as classes de cor foram geradas: `text-red-600`, `text-lime-600`, `text-emerald-700`, `bg-current` e as variantes `dark:text-*-400`. Não houve conferência visual: a `Legenda` ainda não está em rota e o navegador de verificação não conectou nesta máquina.

## 📎 Documentação afetada

- [[glossario]]
- [[CondicoesELegenda]]
- [[2026]] (changelog)
