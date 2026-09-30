---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 108
url: https://github.com/VictorNascimento14/Arcada/pull/108
branch: feat/tratamentos-progresso
tags: [pr, tratamentos, plano, progresso, meterbar]
status: merged
---

# PR #108 — feat(tratamentos): mostrar o progresso do tratamento na ficha e na lista

## 🎯 Contexto

Item 7.8 do [[2026-09-30-plano-da-v1]] (módulo 7 · Plano de tratamento e orçamento): o progresso do tratamento. A conta usa `itensRealizados` (7.1) e o desenho entra na aba `Tratamentos` da ficha (7.3, #97) e na lista dos planos em aberto (7.9, #103). Fecha a issue #106.

## 🔧 Mudanças

- `src/modulos/tratamentos/progresso.ts` — `progressoDoPlano(plano)`: `{ feitos, total, pct }`, com `pct` arredondado e plano sem itens zerado. Com teste.
- `src/modulos/tratamentos/ProgressoDoPlano.tsx` — a `MeterBar` com rótulo de `progressbar` e o texto `X de Y realizados`; não desenha nada sem itens. Com teste.
- `src/modulos/tratamentos/AbaTratamentos.tsx` e `PlanosEmAberto.tsx` — cada linha mostra a barra sob o número de itens. Um teste novo em cada tela confere a barra e o valor.

## 🧠 Decisões técnicas

- **A conta é por item, não por valor**: `realizado/total`, como pede o item, e como o comentário de `itensRealizados` já dizia ("o progresso é isto sobre `plano.itens`"). Um preço ajustado no item não mexe na barra.
- **Plano sem itens não tem barra**, em vez de uma barra em `0%`: sem item não há o que medir. A regra devolve tudo zerado, sem dividir por zero.
- **Todo plano com itens mostra a barra, na situação que for**: o proposto marca `0 de N`, que é verdade, e evita uma regra por situação. Se o ruído pesar na lista, esconder no proposto e no recusado é uma linha no componente.
- **A barra fica dentro da coluna de texto da linha**, sob `N itens`, e não numa linha nova: a estrutura das duas telas e o alinhamento com o nome do plano e do paciente não mudam. `max-w-xs` para ela não esticar em tela larga.
- **`pct` arredondado com `Math.round`** (`1 de 8` é 13): a `MeterBar` já grampeia o valor entre 0 e 100.

## ⚠️ Armadilhas e aprendizados

- **Nada grava `realizadoEm` ainda**: quem marca o item como feito é o atendimento (módulo 9). Até lá a barra só sai de zero por edição do `localStorage`; os testes montam os planos já com `realizadoEm`.
- **A `MeterBar` só preenche quando entra na viewport** (`useInView`): no jsdom o observador nunca dispara e a largura fica em 0%. Os testes leem `aria-valuenow`, que reflete o valor, e não o `width`.
- **O texto ao lado da barra é `aria-hidden`**: o rótulo da `progressbar` já diz o mesmo, e sem isso o leitor de tela repetiria.

## 🧪 Como testar

1. `npm run dev` e, na aba `Tratamentos` de um paciente, crie um plano e adicione dois ou três itens: a linha do plano mostra a barra em zero e `0 de N realizados`. Um plano recém-criado, sem itens, não mostra barra.
2. Em **Tratamentos** (`/tratamentos`), a mesma barra aparece em cada plano em aberto.
3. Ainda não há tela que marque um item como realizado (é do atendimento). Para ver a barra andar: no DevTools, em Application → Local Storage, edite `arcada:planos`, ponha `"realizadoEm": "2026-09-30"` em um dos itens e recarregue. A barra passa a `1 de N` e a largura acompanha.
4. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam.

## 📎 Documentação afetada

- [[ProgressoDoTratamento]]
- [[PlanosEmAberto]]
- [[PlanoDeTratamento]]
- [[FichaDoPaciente]]
- [[TotaisDoPlano]]
- [[2026]] (changelog)
