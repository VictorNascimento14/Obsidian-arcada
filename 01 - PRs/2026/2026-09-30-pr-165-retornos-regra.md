---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 165
url: https://github.com/VictorNascimento14/Arcada/pull/165
branch: feat/retornos-regra
tags: [pr, retornos, regra, datas]
status: aberto
---

# PR #165 — feat(retornos): calcular a data do retorno pelo último atendimento e pelo intervalo do procedimento

## 🎯 Contexto

Item 11.1 do [[2026-09-30-plano-da-v1]] (módulo 11 · Retornos): a regra do retorno. A lista (11.2), o contato (11.3) e o adiamento (11.4) partem dela. A base do retorno é o último atendimento do paciente, como pede o glossário: a volta prevista depois de um procedimento, no prazo que a regra daquele procedimento define. Fecha a issue #161.

## 🔧 Mudanças

- `src/modulos/retornos/regra.ts` — `somarMeses` (o fim do mês ajusta para o último dia), `intervaloDoProcedimento`, `ultimoAtendimento` e `retornoDoPaciente`, mais o padrão de 6 meses e o mapa `INTERVALOS_POR_PROCEDIMENTO`. Regra pura, sem React e sem coleção.
- `src/modulos/retornos/regra.test.ts` — 14 testes: fim do mês e ano bissexto, padrão e mapa, o que conta como atendimento, o mais recente entre as duas fontes, o menor intervalo do dia e o retorno nulo sem atendimento.

## 🧠 Decisões técnicas

- **A base é o último atendimento, não a última consulta**: vale o dia mais recente entre a consulta `concluida` e o item de plano com `realizadoEm`. Faltou, cancelada, agendada e em atendimento não contam.
- **O mapa é do módulo**: o tipo `Procedimento` e o módulo de procedimentos não mudam. Ele vem com um só procedimento, `proc-manutencao-aparelho` (1 mês; o catálogo o chama de manutenção mensal), e o teste confere que todo `id` do mapa existe no catálogo, para um erro de digitação não virar o prazo padrão em silêncio. Nenhum prazo clínico foi inventado.
- **`somarMeses` é do módulo**: o `daquiAMeses` de `tratamentos/parcelas.ts` não é exportado, e o módulo de tratamentos não muda neste PR. São poucas linhas, com o último dia do mês tirado de `Date.UTC(ano, mês, 0)`.
- **Dia com mais de um procedimento**: vale o menor intervalo, o do retorno mais cedo. A consulta sem procedimento não traz intervalo e não encurta o do item feito no mesmo dia.
- **Sem atendimento, sem retorno**: `retornoDoPaciente` devolve `null`; sem base não há data.

## ⚠️ Armadilhas e aprendizados

- `setMonth` transborda (31/08 mais 6 meses vira 03/03) e `new Date("AAAA-MM-DD")` lê UTC e recua um dia no Brasil: a conta é por número, e o último dia do mês vem de `Date.UTC`.

## 🧪 Como testar

1. `npx vitest run src/modulos/retornos --maxWorkers=2`: 14 testes, com o fim do mês (28/02, 29/02 e o ano 2100), o padrão e o mapa por procedimento, o que conta como atendimento e o mais recente entre a consulta e o plano.
2. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[glossario]]
- [[2026-09-30-retorno-conta-do-ultimo-atendimento]]
- [[2026]] (changelog)
