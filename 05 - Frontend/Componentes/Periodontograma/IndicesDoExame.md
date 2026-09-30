---
tipo: funcionalidade
camada: frontend
area: Periodontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, periodontograma, indices]
---

# Índices do exame

## O que é

Os índices do exame periodontal em quatro cartões (`StatCard` do kit), acima da [[GradeDeSondagem]] da aba
Periodonto. É o item 4.6 do [[2026-09-30-plano-da-v1]]. Os números são os que o modelo do PR
[[2026-09-30-pr-088-periodontograma-exame]] já calculava (`indicesDoExame`); a tela só os mostra, sobre o exame de
hoje, e os recalcula a cada valor digitado ou marcado.

## Onde está no código

- `src/modulos/periodontograma/IndicesDoExame.tsx` — a seção `Índices do exame` com os quatro `StatCard`.
- `src/modulos/periodontograma/indices.ts` — `cartoesDosIndices`: rótulo, valor formatado, apoio, ícone e tom de
  cada cartão (regra pura, com teste).
- `src/modulos/periodontograma/AbaPeriodonto.tsx` — monta a seção entre o cabeçalho e a grade.
- `src/modulos/periodontograma/exame.ts` — `indicesDoExame`, o cálculo.

## Comportamento

- **Os quatro cartões**, nesta ordem:
  - **Sangramento à sondagem** — o percentual dos sítios medidos que sangram (`33,3%`, `50%`).
  - **Profundidade média (mm)** — a média das profundidades dos sítios medidos, sempre com uma casa (`3,2`, `3,0`).
  - **Profundidade ≥ 4 mm** — quantos sítios têm profundidade de 4 mm ou mais, com o apoio `de 192 sítios medidos`.
  - **Inserção ≥ 3 mm** — quantos sítios têm nível de inserção clínica (profundidade mais margem) de 3 mm ou
    mais; só entram os sítios com as duas medidas.
- **A base é o sítio medido**: o que tem profundidade, em dente presente. O apoio de cada cartão diz quantos são
  (`6 sítios medidos`). Sangramento marcado num sítio sem profundidade não conta.
- **Sem nenhum sítio medido**, o percentual e a média são um traço (`—`), as contagens são `0` e o apoio diz
  `Nenhum sítio medido`.
- **Atualização**: a cada mudança do exame, sem botão. Os números não animam: mudam a cada tecla digitada, e um
  contador animado tocaria a cada dígito.
- **Sem faixa de cor por limite**: o tom de cada cartão é fixo (vermelho no sangramento, o verde do kit nos outros).
  O app não sugere conduta clínica: dizer que um número está "alto" é decisão de quem examina.
- **Layout**: dois cartões por linha no celular e quatro a partir de 1024 px.

## Movimento e micro-interações

É o do `StatCard`: o cartão entra subindo quando aparece na tela e levanta no hover. Nada foi acrescentado.

## Limites conhecidos

- **Só o exame de hoje**: a diferença entre dois exames é o item 4.7.
- **Dente ausente ainda não se marca na grade**: o modelo o deixaria fora dos índices, mas hoje só o sítio sem
  medida fica de fora.
- **Não há meta nem limite de referência** nos cartões, de propósito.

## Histórico de mudanças

- [[2026-09-30-pr-164-periodontograma-indices]] — os quatro cartões de índices acima da grade.
