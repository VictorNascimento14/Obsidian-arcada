---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 190
url: https://github.com/VictorNascimento14/Arcada/pull/190
branch: feat/painel-faturamento
tags: [pr, painel, faturamento, financeiro]
status: merged
---

# PR #190 — feat(painel): mostrar o faturamento por semana em barras

## 🎯 Contexto

Item 13.5 do [[2026-09-30-plano-da-v1]] (módulo 13 · Painel): o faturamento por semana em barras com `MeterBar`, na escala `primary-800..500`, sobre a mesma tela dos itens 13.1 ([[ConsultasDeHoje]]), 13.2 ([[IndicadoresDoMes]]) e 13.3 ([[TratamentosEmAberto]]). Lê o `pagoEm` que a baixa do Financeiro grava ([[ContasAReceber]]). O 13.4 (retornos da semana e aniversariantes) não faz parte deste PR. Fecha a issue #185.

## 🔧 Mudanças

- `src/modulos/painel/porSemana.ts` — `faturamentoPorSemana`, `pctDaBarra`, `degrauDaBarra` e `SEMANAS_NO_PAINEL`: as semanas, o recebido em cada uma e o tamanho e o degrau de cor da barra. Reaproveita `inicioDaSemana` e `somarDias` da agenda.
- `src/modulos/painel/porSemana.test.ts` — 8 testes: as semanas de segunda a domingo, o padrão de seis, a soma com os limites da janela (segunda, domingo, antes, depois, em aberto), o domingo que fecha a semana, a virada do ano, o tamanho da barra sem dividir por zero e os degraus.
- `src/modulos/painel/FaturamentoPorSemana.tsx` — o bloco: título, subtítulo e uma lista com uma linha por semana (período, valor e `MeterBar`), ou o aviso de que não houve recebimento.
- `src/modulos/painel/FaturamentoPorSemana.test.tsx` — 3 testes de tela: o período e o valor de cada semana, o tamanho e a cor das barras e o estado sem recebimento.
- `src/modulos/painel/Painel.tsx` — o bloco entra ao lado das consultas de hoje, em duas colunas (3fr e 2fr) a partir de `xl`; abaixo disso, um sob o outro.

## 🧠 Decisões técnicas

- **Janela móvel de seis semanas, não as semanas do mês**: a semana não respeita a virada do mês (a de 28/09 a 04/10 é metade setembro, metade outubro), e o bloco mostra a tendência; o total do mês já está nos indicadores. Seis linhas cabem no celular e bastam para ver se o caixa sobe ou cai.
- **Semana de segunda a domingo, a da agenda**: `inicioDaSemana` e `somarDias` de `agenda/dias.ts`, que andam pelos campos da data (sem somar milissegundos, então sem erro de horário de verão), em vez de refazer a conta. O domingo fecha a semana.
- **Recebido por `pagoEm`**, como nos indicadores do mês: a parcela paga em atraso conta na semana em que o dinheiro entrou, e a em aberto não conta. Soma em centavos inteiros.
- **O tamanho da barra é o valor sobre o da maior semana da janela**, não sobre uma meta: o app não tem meta. Sem nenhum valor na janela a divisão nem acontece (`pctDaBarra` devolve 0) e o bloco diz que não houve recebimento, em vez de seis barras vazias.
- **Escala `primary-800..500` em quatro degraus de 25%**: a semana mais forte é a mais escura, e o degrau mais claro é `primary-500`, porque abaixo dele o preenchimento some no trilho (invariante 3). As classes são literais no fonte (invariante 5), num vetor indexado pelo degrau.
- **O texto carrega o dado e a barra é desenho**: cada linha traz período e valor em texto, e o `MeterBar` fica sem `label` (sem `role="progressbar"`), para o leitor de tela não ouvir cada valor duas vezes.
- **Da mais antiga à mais recente**, com a semana de hoje por último, em negrito e marcada `esta semana`: ela está em curso, então tende a ser a mais baixa até a semana terminar.
- **Duas colunas só a partir de `xl`**: a coluna da direita fica com 2/5 da largura útil, e o período mais o valor não cabem numa linha abaixo disso; antes do `xl` o bloco desce para baixo das consultas. `items-start` impede o cartão menor de esticar até a altura do maior.

## ⚠️ Armadilhas e aprendizados

- O `MeterBar` e o `GlassCard` só entram depois de aparecer na janela (`useInView`): numa captura de tela com a janela mais baixa que a página, as barras de baixo saem vazias e os cartões, invisíveis (`opacity-0` até entrar). Para conferir a tela inteira, a janela do navegador precisa ser alta o bastante.
- O teste lê o tamanho final da barra em `--w`, a variável que o `MeterBar` usa na animação de entrada: a largura em si fica em 0% até o `IntersectionObserver` disparar, e o jsdom não o dispara.

## 🧪 Como testar

1. `npx vitest run src/modulos/painel --maxWorkers=2`: 46 testes, 11 deles novos. Oito da regra (as semanas de segunda a domingo, o padrão de seis, a soma com os limites da janela, o domingo que fecha a semana, a virada do ano, o tamanho e o degrau da barra) e três da tela (período e valor de cada semana, tamanho e cor das barras, o estado sem recebimento).
2. `npm run dev` e abrir `/`: a demonstração não traz lançamentos, então o bloco diz `Nenhuma parcela recebida nas últimas 6 semanas.`. Com parcelas que receberam baixa (Financeiro, Contas a receber), as barras aparecem, com a semana de hoje marcada.
3. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[PainelDoConsultorio]]
- [[FaturamentoPorSemana]]
- [[2026]] (changelog)
