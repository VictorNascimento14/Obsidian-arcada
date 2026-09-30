---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 177
url: https://github.com/VictorNascimento14/Arcada/pull/177
branch: feat/painel-indicadores
tags: [pr, painel, indicadores, financeiro, agenda]
status: merged
---

# PR #177 — feat(painel): mostrar o faturamento recebido, as consultas e a taxa de faltas do mês

## 🎯 Contexto

Item 13.2 do [[2026-09-30-plano-da-v1]] (módulo 13 · Painel): faturamento recebido, consultas e taxa de faltas do mês, com `StatCard` e `AnimatedNumber`. Segue o 13.1 ([[ConsultasDeHoje]]) sobre a mesma tela; os tratamentos em aberto (13.3) e o faturamento por semana (13.5) vêm depois. Fecha a issue #174.

## 🔧 Mudanças

- `src/modulos/painel/indicadores.ts` — `mesDe`, `faturamentoRecebido`, `consultasDoMes` e `taxaDeFaltas`: a regra pura de cada número do mês.
- `src/modulos/painel/indicadores.test.ts` — 10 testes: o mês de um dia e de um horário; o faturamento por `pagoEm` (em atraso, adiantada, em aberto, mês vizinho, sem lançamento); as consultas sem a cancelada; a taxa (1/3, o que fica fora da base, 0, 1 e `null`).
- `src/modulos/painel/IndicadoresDoMes.tsx` — o bloco: título com o mês por extenso e uma lista com um `StatCard` por indicador, o número em `AnimatedNumber`.
- `src/modulos/painel/IndicadoresDoMes.test.tsx` — 3 testes de tela: os três valores finais, a virada do mês e o mês sem dado.
- `src/modulos/painel/Painel.tsx` — monta o bloco entre as boas-vindas e as consultas de hoje, com o `hoje` do relógio do painel.

## 🧠 Decisões técnicas

- **Recebido é por `pagoEm`, não por vencimento**: a parcela paga em atraso conta no mês em que o dinheiro entrou, a adiantada no mês do pagamento, e a em aberto não conta, mesmo vencida. Soma em centavos inteiros, por `somarCentavos`.
- **Consultas do mês sem as canceladas**: a cancelada libera o horário e não conta, como na agenda e no 13.1. As que ainda vão acontecer entram: o número é a carga do mês, não só o que já foi feito.
- **Taxa de 0 a 1, com `null` sem base**: faltou ÷ (concluída + faltou). Agendada, confirmada, em atendimento e cancelada ficam fora da base. Sem concluída nem faltou a taxa é `null` e a tela mostra "—" com a explicação: nenhuma falta em nenhuma consulta não é 0%, e 0% só aparece quando houve consulta concluída e nenhuma falta.
- **O mês sai do dia**: `mesDe(hoje)`, os sete primeiros caracteres, com o `hoje` do relógio do `Painel` (`diaISO`). Na noite do último dia do mês o painel segue no mês certo; com `toISOString()` já estaria no seguinte.
- **Percentual com uma casa** (`Intl.NumberFormat` em `percent`, `maximumFractionDigits: 1`): 1 falta em 8 é 12,5%, e não 13%; 0% e 100% saem sem decimal.
- **Sem `useMemo`**: são três passadas sobre listas pequenas e o painel re-renderiza no máximo uma vez por minuto.
- **Cada cartão é um item de lista** (`ul` > `li`): o leitor de tela anuncia a lista de três indicadores, e o teste acha os cartões por papel, sem depender de classe.

## ⚠️ Armadilhas e aprendizados

- `AnimatedNumber` troca o texto visível ~60 vezes por segundo durante a contagem: o valor estável é o do par `sr-only`, e é ele que o teste lê.
- O `Intl` de moeda separa `R$` do número com espaço não separável (U+00A0), o de percentual não separa o `%` (`33,3%`): o teste monta o valor esperado com `formatarReais` em vez de digitar o texto.

## 🧪 Como testar

1. `npx vitest run src/modulos/painel --maxWorkers=2`: 27 testes, 13 deles novos. Dez da regra (mês, faturamento com os limites do mês, consultas, taxa de faltas e a base vazia) e três da tela (valores finais, virada do mês, mês sem dado).
2. `npm run dev` e abrir `/`: sob as boas-vindas aparece a linha Indicadores do mês, com o mês por extenso. Sem parcela paga o faturamento é R$ 0,00; sem consulta concluída nem com falta no mês a taxa mostra "—" e diz por quê.
3. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[PainelDoConsultorio]]
- [[IndicadoresDoMes]]
- [[2026]] (changelog)
