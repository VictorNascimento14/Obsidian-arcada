---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 164
url: https://github.com/VictorNascimento14/Arcada/pull/164
branch: feat/perio-indices
tags: [pr, periodontograma, indices, statcard]
status: merged
---

# PR #164 — feat(periodontograma): mostrar os índices do exame em cartões

## 🎯 Contexto

Item 4.6 (Índices do exame) do [[2026-09-30-plano-da-v1]]. Mostra o que o modelo do item 4.1 (PR #88, `indicesDoExame`) já calcula, sobre o exame de hoje que a grade dos itens 4.2 a 4.4 preenche. A comparação entre exames é o item 4.7. Fecha a issue #162.

## 🔧 Mudanças

- `src/modulos/periodontograma/indices.ts` (+ teste): `cartoesDosIndices`, que formata os índices de `indicesDoExame` — percentual, média com uma casa, contagens, traço sem dado e a linha de apoio com os sítios medidos.
- `src/modulos/periodontograma/IndicesDoExame.tsx`: a seção com os quatro `StatCard`, em duas colunas no celular e quatro a partir de `lg`.
- `src/modulos/periodontograma/AbaPeriodonto.tsx` (+ teste): a aba passa a ter três blocos — cabeçalho, índices e grade —, e os índices se recalculam a cada mudança do exame.

## 🧠 Decisões técnicas

- **A formatação é regra pura, à parte do componente** (`cartoesDosIndices`): arredondamento, traço e plural têm teste sem depender da tela; o componente só entrega cada cartão ao `StatCard`.
- **Percentual com uma casa só quando não é inteiro** (`33,3%`, `50%`): arredondar para inteiro mostraria `0%` para um sítio sangrando em duzentos e cinquenta, e quem lê pensaria que não houve sangramento. A média leva sempre uma casa (`3,0`).
- **Sem faixa de cor por limite**: o tom de cada cartão é fixo (vermelho no sangramento, o verde padrão do kit nos outros), e um teste garante que ele não muda com o valor. O app não sugere conduta clínica; colorir "acima do aceitável" seria decidir por quem examina.
- **Sem `AnimatedNumber`**: os números mudam a cada tecla digitada, e o contador animado tocaria a cada dígito.
- **A base aparece em cada cartão** (`192 sítios medidos`): o percentual e a média só valem sobre o que foi medido, e o número de sítios muda a leitura. Sem nenhum sítio medido, o apoio diz `Nenhum sítio medido`, e não há percentual nem média.
- **Três blocos em vez de um cartão só**: o `StatCard` já é um cartão de vidro, e pô-lo dentro do cartão da grade seria vidro dentro de vidro.

## ⚠️ Armadilhas e aprendizados

- Sangramento marcado num sítio sem profundidade não entra no percentual (regra do modelo): na tela a marca fica ligada e o número não muda até a profundidade existir. Há teste.
- O `StatCard` usa `GlassCard`, que entra por `useInView`; o teste roda porque `src/test/setup.ts` já tem um `IntersectionObserver` de mentira.
- A nota [[GradeDeSondagem]] segue por atualizar em lote (apontado nos PRs #146 e #156): ela ainda descreve só profundidade e margem e a grade sem os cartões.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/periodontograma` — `indices.test.ts` (a formatação) e `AbaPeriodonto.test.tsx` (os cartões seguem o exame).
2. `npm run dev`, aba **Periodonto** de um paciente sem exame hoje: quatro cartões, com `—` no sangramento e na média, `0` nas duas contagens e `Nenhum sítio medido`.
3. Digitar profundidade 3 e margem 0 no MV do dente 16, profundidade 5 no V, e marcar o sangramento do MV: sangramento 50%, média 4,0, profundidade ≥ 4 mm 1 (`de 2 sítios medidos`) e inserção ≥ 3 mm 1.
4. Marcar o sangramento de um sítio que não tem profundidade: o percentual não muda.
5. Com a largura de um celular (390 px) os cartões ficam em duas colunas; a partir de 1024 px, em quatro.

## 📎 Documentação afetada

- [[IndicesDoExame]]
- [[GradeDeSondagem]]
- [[2026-09-30-pr-088-periodontograma-exame]]
- [[2026]] (changelog)
