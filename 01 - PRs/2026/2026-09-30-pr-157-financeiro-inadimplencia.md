---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 157
url: https://github.com/VictorNascimento14/Arcada/pull/157
branch: feat/financeiro-inadimplencia
tags: [pr, financeiro, inadimplencia, parcela]
status: aberto
---

# PR #157 — feat(financeiro): calcular a inadimplência por paciente com os dias de atraso e o total vencido

## 🎯 Contexto

Item 10.4 do [[2026-09-30-plano-da-v1]] (módulo 10 · Financeiro): a inadimplência. Parte da situação `vencida` da parcela (10.2, #135) e das baixas (10.3, #147); `inadimplentes` agrupa por paciente o que já está nas parcelas. Fecha a issue #151.

## 🔧 Mudanças

- `src/modulos/financeiro/inadimplencia.ts` — `diasDeAtrasoDaParcela` e `inadimplentes`: só a parcela em aberto vencida entra; por paciente, quantas são, a soma e os dias da mais antiga. Regra pura, com teste (virada de mês e de ano, ano bissexto, ordem, paga e vence hoje fora).
- `Inadimplentes.tsx` — o cartão da lista, com o resumo e o estado vazio. `Inadimplentes.test.tsx` cobre a ordem, a conta e os estados.
- `PaginaFinanceiro.tsx` — monta o cartão entre as contas a receber e os planos sem parcelas.

## 🧠 Decisões técnicas

- **`vencida` é a definição.** A inadimplência reusa `situacaoDaParcela`, então o que é atraso aqui é o mesmo da pílula `Vencida` das contas a receber: a parcela que vence hoje ainda não conta, e a paga sai da conta.
- **Os dias de atraso do paciente são os da parcela vencida mais antiga**: é há quanto tempo ele deve. Uma parcela vencida mais recente não abrevia isso.
- **Dias por número do dia em UTC** (`Date.UTC` com números): dias de calendário exatos, sem o fuso e sem `new Date` sobre texto, que no Brasil recuaria um dia.
- **Do atraso mais antigo ao mais recente**, com o maior total e o nome no desempate: o primeiro da lista é quem deve há mais tempo.
- **A regra vem com uma tela pequena.** O item é `puro` no plano, mas uma regra sem uso não se confere no app; o cartão é a menor tela que a mostra, e, se o time preferir só a regra, ele sai sem mexer nela.
- **O cartão fica entre as contas a receber e os planos sem parcelas**; o cabeçalho da página segue `Contas a receber`.

## ⚠️ Armadilhas e aprendizados

- **Subtrair dois `Date` locais não dá dias exatos** num fuso com horário de verão: a diferença deixa de ser múltipla de 24 h no dia da mudança. `Date.UTC` com números não tem esse dia.

## 🧪 Como testar

1. `npm run dev`: em **Financeiro**, gere as parcelas de um plano aprovado com o 1º vencimento dois meses atrás, para haver parcelas vencidas.
2. O cartão **Inadimplência** lista o paciente com `N parcelas vencidas · X dias de atraso` (os dias da parcela vencida mais antiga) e o total vencido; o resumo diz `1 paciente inadimplente, com R$ … vencidos`.
3. Dê baixa na parcela mais antiga (**Dar baixa**): o total vencido cai e os dias de atraso passam a ser os da parcela vencida seguinte. Sem parcela vencida, o cartão diz `Nenhum paciente inadimplente.`
4. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[Inadimplencia]]
- [[SituacaoDaParcela]]
- [[ContasAReceber]]
- [[PlanosSemParcelas]]
- [[glossario]]
- [[2026]] (changelog)
