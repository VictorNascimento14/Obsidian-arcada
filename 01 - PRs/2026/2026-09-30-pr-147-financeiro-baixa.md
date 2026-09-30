---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 147
url: https://github.com/VictorNascimento14/Arcada/pull/147
branch: feat/financeiro-baixa
tags: [pr, financeiro, baixa, parcela, pagamento]
status: aberto
---

# PR #147 — feat(financeiro): dar baixa em uma parcela com a forma e a data do pagamento

## 🎯 Contexto

Item 10.3 do [[2026-09-30-plano-da-v1]] (módulo 10 · Financeiro): a baixa de parcela. Escreve `pagoEm` e `forma`, campos do tipo `Lancamento` (#25), nas parcelas geradas no 10.1 (#127) e listadas no 10.2 (#135). Fecha a issue #139.

## 🔧 Mudanças

- `src/modulos/financeiro/formas.ts` — `ROTULO_DA_FORMA` e `FORMAS_DE_PAGAMENTO`.
- `src/modulos/financeiro/lancamentos.ts` — `darBaixa`: confere a forma e o dia (existe e não passa de hoje) e grava `pagoEm` e `forma` na parcela. Com teste.
- `BaixaDaParcela.tsx` e `FormularioDaBaixa.tsx` — o botão e o modal. `BaixaDaParcela.test.tsx` cobre o modal.
- `ContasAReceber.tsx` e `AbaFinanceiro.tsx` — o botão nas parcelas em aberto, o dia e a forma nas pagas e a linha que quebra em tela estreita; os testes de tela ganham o fluxo da baixa.
- `PlanosSemParcelas.tsx` — uma classe (`basis-40`) no bloco do paciente, para a linha quebrar no celular (commit à parte).

## 🧠 Decisões técnicas

- **A baixa é do valor inteiro.** O formulário não tem valor, e `darBaixa` só recebe forma e dia. Baixa parcial (pagar parte da parcela) fica fora da v1, como o item pede.
- **O dia do pagamento não passa de hoje.** Pagamento que ainda não aconteceu não é baixa, e um dia futuro no caixa do dia (10.5) mostraria dinheiro que não entrou. O campo tem `max` de hoje, e `darBaixa` confere de novo, com `hoje` de parâmetro para o teste escolher o dia.
- **Parcela paga não recebe outra baixa.** `darBaixa` lança se `pagoEm` já existe: sobrescrever apagaria o registro do pagamento. Desfazer é o estorno (10.8).
- **Um botão, dois lugares.** `BaixaDaParcela` guarda o estado do modal e serve à lista e à aba, para a baixa ter uma receita só.
- **As formas em um arquivo.** `formas.ts` guarda os nomes, para o caixa do dia e o recibo (10.5 e 10.7) usarem os mesmos.
- **Dia que existe, sem `Date` novo.** `darBaixa` reaproveita `somarDias` da agenda: `2026-02-30` volta como `2026-03-02` e reprova.

## ⚠️ Armadilhas e aprendizados

- **`darBaixa` devolve erros de campo, mas lança no engano de quem chama** (parcela que não existe ou já paga): a tela nem oferece esses casos.
- **O cartão abaixo da dobra não aparece em captura de página inteira** (o `GlassCard` só se revela ao entrar na viewport): a verificação visual rolou até cada cartão antes de capturar ([[2026-09-30-captura-de-pagina-inteira-nao-revela-o-cartao-abaixo-da-dobra]]).
- **A verificação visual no celular (390 px) achou um defeito do PR #127**: `min-w-0 flex-1` numa linha com `flex-wrap` deixa o bloco do nome encolher até quase zero em vez de quebrar a linha (o nome virava `Ana Beat…`). O bloco ganhou uma largura de partida (`basis-40`); as linhas novas já nascem assim.
- **O dia dos testes de tela é fixado com `vi.useFakeTimers({ toFake: ["Date"] })`**: só o `Date` é falso, e o resto do Testing Library segue com os timers reais.

## 🧪 Como testar

1. `npm run dev`: em **Financeiro**, gere as parcelas de um plano aprovado (`Gerar parcelas`).
2. Em **Contas a receber**, clique em **Dar baixa** numa parcela: o modal mostra o valor e o vencimento, sem campo de valor. Sem escolher a forma, ou com a data depois de hoje, o campo mostra o erro e nada é gravado.
3. Escolha `Pix` e confirme: aparece `Baixa registrada`, a parcela vai para o fim da lista como `Paga`, com `pago em … · Pix`, e o resumo deixa de contá-la.
4. Abra a ficha do paciente, aba **Financeiro**: a parcela paga não tem o botão; as em aberto têm e dão baixa do mesmo jeito.
5. No celular (390 px), `Planos sem parcelas` mostra o nome inteiro e leva o total e o botão para a linha de baixo.
6. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[BaixaDaParcela]]
- [[ContasAReceber]]
- [[SituacaoDaParcela]]
- [[GeracaoDeParcelas]]
- [[FichaDoPaciente]]
- [[glossario]]
- [[2026-09-30-captura-de-pagina-inteira-nao-revela-o-cartao-abaixo-da-dobra]]
- [[2026]] (changelog)
