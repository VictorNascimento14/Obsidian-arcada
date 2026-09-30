---
tipo: aprendizado
data: 2026-09-30
contexto: PR #165 — regra de retorno por procedimento
tags: [aprendizado, retornos, datas]
---

# O retorno conta do último atendimento, e o fim do mês ajusta

Origem: [[2026-09-30-pr-165-retornos-regra]]. Termo no [[glossario]]: **Retorno**.

## O que a regra diz

- **A base é o último atendimento do paciente**: o dia mais recente entre a consulta `concluida` (pelo dia do
  `inicio`) e o item de plano com `realizadoEm`. Consulta agendada, confirmada, em atendimento, que faltou ou foi
  cancelada não é atendimento; item ainda a fazer também não.
- **O intervalo é o do procedimento feito nesse dia**: o mapa `INTERVALOS_POR_PROCEDIMENTO`
  (`src/modulos/retornos/regra.ts`) dá os meses; o procedimento sem regra usa 6.
- **A consulta sem procedimento** (`procedimentoId` é opcional) não traz intervalo: o dia que só tem ela usa o
  padrão, e no dia em que há item de plano ela não encurta o prazo do item.
- **Vários procedimentos no mesmo dia**: vale o menor intervalo, o do retorno mais cedo.
- **Sem atendimento não há retorno**: quem nunca foi atendido não tem de onde contar.

## O fim do mês

`31/08` mais 6 meses é `28/02` (ou `29/02` em ano bissexto), não `03/03`. A conta é por número
(`ano × 12 + mês + meses`), e o último dia do mês de chegada vem de `Date.UTC(ano, mês, 0)`. Cada retorno parte do
atendimento real, nunca do retorno anterior, então o dia 31 não se arrasta para o 28 nos meses seguintes.

## Como evitar o erro

- `Date#setMonth` transborda: `31/08` com `setMonth(+6)` cai em `03/03`. Some meses por número.
- `new Date("AAAA-MM-DD")` lê UTC e, no Brasil, recua um dia: nunca monte o dia por texto assim. O dia de hoje sai
  de `diaISO`.
