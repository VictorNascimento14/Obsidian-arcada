---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 135
url: https://github.com/VictorNascimento14/Arcada/pull/135
branch: feat/financeiro-a-receber
tags: [pr, financeiro, parcela, situacao, lista]
status: aberto
---

# PR #135 — feat(financeiro): listar as contas a receber com a situação de cada parcela

## 🎯 Contexto

Item 10.2 do [[2026-09-30-plano-da-v1]] (módulo 10 · Financeiro): as contas a receber. Lê a coleção `lancamentos`, que o 10.1 (#127) passou a preencher, e a coleção `pacientes`; o dia de hoje vem de `diaISO`. Fecha a issue #132.

## 🔧 Mudanças

- `src/modulos/financeiro/situacao.ts` — `situacaoDaParcela(parcela, hoje)`, o tipo `SituacaoDaParcela` e os rótulos. Regra pura, com teste.
- `src/modulos/financeiro/contasAReceber.ts` — `contasAReceber(lancamentos, pacientes, hoje)`: junta o paciente e a situação de cada parcela e ordena (em aberto por vencimento, pagas no fim). Com teste.
- `ContasAReceber.tsx` e `SituacaoDaParcelaBadge.tsx` — o cartão da lista, com o resumo, e a pílula da situação. `ContasAReceber.test.tsx` cobre a ordem, o resumo e os estados vazios.
- `PaginaFinanceiro.tsx` — passa a montar os dois cartões: contas a receber e, abaixo, os planos sem parcelas.
- `AbaFinanceiro.tsx` — cada parcela ganha a pílula da situação; o texto passa de `Vence em` para `Vencimento em`, que serve às vencidas e às pagas.
- `PlanosSemParcelas.test.tsx` — o teste que era da página passa a montar o cartão direto (`git mv`), já que a página agora tem dois.

## 🧠 Decisões técnicas

- **As pagas ficam no fim da lista, não fora dela.** O item pede a situação `paga` na lista, e a soma do resumo conta só as em aberto — a definição de contas a receber do [[glossario]]. Ordenar tudo pelo vencimento poria as pagas antigas no topo, à frente do que ainda falta receber.
- **`paga` vence qualquer comparação de datas**: quem tem `pagoEm` está paga, mesmo com o vencimento no futuro ou depois de vencida.
- **`hoje` é parâmetro da regra**, e a tela o lê de `diaISO(new Date())`. O teste de tela roda às 23:30 do dia 15 para fixar que o dia é o local, não o de UTC.
- **Vence hoje não é vencida**: a parcela só vira vencida no dia seguinte, o que o teste da virada de mês amarra.
- **Contas a receber acima de planos sem parcelas** na página: o que o consultório tem a receber é o uso diário; gerar parcelas é a exceção.

## ⚠️ Armadilhas e aprendizados

- **O dia de hoje é o da última renderização**: a tela aberta na virada da meia-noite só corrige a situação ao se redesenhar. É um teto conhecido, com comentário `ponytail:` no componente; se pesar, um relógio que renove ao virar o dia.
- **A nota [[PlanosSemParcelas]] descreve a aba da ficha com `Vence em` e `Em aberto`**, texto que este PR troca. A nota nova [[ContasAReceber]] traz a descrição atual; a antiga fica para o orquestrador ajustar, porque `extra/` só cria nota nova.
- **`vi.useFakeTimers({ toFake: ["Date"] })` nos testes de tela**: só o `Date` é falso, e o resto do Testing Library segue com os timers reais.
- **Não houve verificação visual no navegador** (o Chrome não conecta nesta máquina): as cores das pílulas e a linha em telas estreitas foram conferidas só por teste de tela e pelo build.

## 🧪 Como testar

1. `npm run dev`: em **Financeiro**, gere 4 parcelas de um plano aprovado com o 1º vencimento dois meses atrás, no mesmo dia do mês de hoje.
2. No cartão **Contas a receber**, as quatro aparecem pelo vencimento: as duas primeiras `Vencida`, a terceira `Vence hoje` e a quarta `A vencer`, com o resumo `4 parcelas em aberto, somando R$ …`.
3. Abra a ficha do paciente, aba **Financeiro**: as mesmas quatro parcelas, com as mesmas pílulas.
4. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[ContasAReceber]]
- [[SituacaoDaParcela]]
- [[PlanosSemParcelas]]
- [[FichaDoPaciente]]
- [[glossario]]
- [[2026]] (changelog)
