---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, parcela, situacao]
---

# Situação da parcela

## O que é

A regra que diz em que pé está uma parcela num dia: **paga**, **vencida**, **vence hoje** ou **a vencer**. Não tem
tela própria: a lista [[ContasAReceber]] e a aba Financeiro da [[FichaDoPaciente]] mostram o resultado numa pílula.
Bate com o [[glossario]]: inadimplente é a parcela cujo vencimento passou sem baixa, isto é, a `vencida`.

## Onde está no código

- `src/modulos/financeiro/situacao.ts` — `situacaoDaParcela(parcela, hoje)`, o tipo `SituacaoDaParcela` e
  `ROTULO_SITUACAO_DA_PARCELA`.
- `SituacaoDaParcelaBadge.tsx` — a pílula: a vencer, vence hoje e paga em tons do kit, vencida em vermelho. A cor
  ajuda; quem diz é o texto.

## Comportamento

- **Paga** vem primeiro: quem tem `pagoEm` está paga, seja qual for o vencimento — adiantada, no dia ou depois de
  vencida.
- Sem baixa, compara o vencimento com `hoje`: antes de hoje é **vencida**; igual, **vence hoje**; depois, **a vencer**.
  Vencer hoje ainda não é atraso: a parcela só vira vencida no dia seguinte.
- **`hoje` vem de fora**, em `AAAA-MM-DD`; a tela o lê de `diaISO(new Date())`, nunca de `toISOString()`, que à noite,
  no Brasil, já devolve o dia seguinte e marcaria como vencida a parcela que vence hoje. As datas são texto nesse
  formato, então comparar os textos compara os dias, sem `Date`.

## Histórico de mudanças

- [[2026-09-30-pr-135-financeiro-a-receber]] — `situacaoDaParcela` e a pílula da situação.
