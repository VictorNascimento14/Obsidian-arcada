---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: /financeiro
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, parcela, lista, situacao]
---

# Contas a receber

## O que é

O cartão principal da tela `/financeiro`: as parcelas de todos os pacientes, cada uma com a situação de hoje — a
vencer, vence hoje, vencida ou paga —, as em aberto primeiro. Fica acima de [[PlanosSemParcelas]], que gera as
parcelas. A mesma situação aparece na aba **Financeiro** da [[FichaDoPaciente]].

## Onde está no código

- `src/modulos/financeiro/ContasAReceber.tsx` — o cartão. `PaginaFinanceiro.tsx` monta os dois cartões.
- `contasAReceber.ts` — `contasAReceber(lancamentos, pacientes, hoje)`: o paciente de cada parcela e a ordem.
- `situacao.ts` — a regra da situação ([[SituacaoDaParcela]]). `SituacaoDaParcelaBadge.tsx` — a pílula.
- `AbaFinanceiro.tsx` — a aba da ficha, agora com a pílula.

## Comportamento

- **Ordem**: as em aberto primeiro, pelo vencimento — então as vencidas, a que vence hoje e as a vencer saem na
  ordem em que pedem atenção — e as pagas no fim; no mesmo dia, pelo nome do paciente. Pelo vencimento puro, as pagas
  antigas ficariam no topo, à frente do que ainda falta receber.
- **Cada linha**: avatar, nome do paciente, `Vencimento em 15/10/2026` (mais ` · pago em 14/10/2026` nas pagas), a
  pílula da situação e o valor. Parcela de paciente que já não existe aparece como `Paciente não encontrado`.
- **Resumo**: `2 parcelas em aberto, somando R$ 50,00` (ou `1 parcela`). As pagas não entram na soma, que é a
  definição de contas a receber do [[glossario]]. Sem nenhuma em aberto: `Nenhuma parcela em aberto.`
- **Sem parcelas**: `Nenhuma parcela ainda. Elas nascem quando um plano aprovado é parcelado, em Planos sem parcelas.`
- **A aba Financeiro da ficha** lista só as parcelas do paciente, do vencimento mais antigo ao mais novo, com a mesma
  pílula: `Vencimento em 15/10/2026`, a linha `Pago em …` quando já houve a baixa, a situação e o valor. Esta
  descrição vale no lugar da que a nota [[PlanosSemParcelas]] dá para a aba (`Vence em` e `Em aberto`).
- O dia de hoje é o de quando a tela é desenhada (`diaISO(new Date())`): aberta na virada da meia-noite, a tela só
  corrige a situação ao se redesenhar.

## Movimento e micro-interações

Cartão de vidro, sem animação própria; a pílula muda de cor com a situação, e o texto é quem a diz.

## Histórico de mudanças

- [[2026-09-30-pr-135-financeiro-a-receber]] — o cartão de contas a receber em `/financeiro` e a situação de cada parcela, também na aba da ficha.
