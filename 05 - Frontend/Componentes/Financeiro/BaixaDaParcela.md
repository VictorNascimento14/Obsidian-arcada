---
tipo: funcionalidade
camada: frontend
area: Financeiro
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, financeiro, baixa, parcela]
---

# Baixa da parcela

## O que é

O registro de que uma parcela foi paga: o dia e a forma do pagamento, gravados na própria parcela (`pagoEm` e
`forma`), que passa a **paga** ([[SituacaoDaParcela]]). É o botão **Dar baixa** de toda parcela em aberto — na lista
[[ContasAReceber]] e na aba **Financeiro** da [[FichaDoPaciente]] — e o modal que ele abre. A baixa é sempre da
parcela inteira: **baixa parcial não entra na v1**. Termos do [[glossario]]: Baixa e Parcela.

## Onde está no código

- `src/modulos/financeiro/lancamentos.ts` — `darBaixa(lancamentoId, campos, hoje?)` e os tipos `CamposDaBaixa` e
  `ErrosDaBaixa`; o arquivo também tem a geração das parcelas ([[GeracaoDeParcelas]]).
- `formas.ts` — `ROTULO_DA_FORMA` e `FORMAS_DE_PAGAMENTO`: dinheiro, Pix, cartão de débito e cartão de crédito.
- `BaixaDaParcela.tsx` — o botão e o estado do modal. `FormularioDaBaixa.tsx` — o modal.
- `ContasAReceber.tsx` e `AbaFinanceiro.tsx` — cada uma põe o botão nas parcelas em aberto.

## Comportamento

- **Botão**: só nas parcelas em aberto; a paga não tem. O nome acessível leva o vencimento e, na lista, o paciente:
  `Dar baixa na parcela de 15/10/2026 de Ana Exemplo`.
- **Modal**: mostra o valor e o vencimento da parcela, o aviso `A baixa quita a parcela inteira.` e dois campos —
  **Forma de pagamento** (as quatro formas; parte de `Escolha a forma`) e **Data do pagamento** (parte de hoje, e o
  campo não deixa escolher depois de hoje). Não há campo de valor. **Dar baixa** grava; **Cancelar**, o Escape e o
  clique fora fecham sem gravar.
- **Erros no campo**: forma não escolhida (`Escolha a forma de pagamento.`), dia vazio ou que não existe (`Informe o
  dia do pagamento.`) e dia depois de hoje (`O pagamento não pode ser depois de hoje.`). Com erro, nada é gravado e o
  modal fica aberto.
- **`darBaixa`** repete a conferência — a validação da tela é conforto, a da função é a regra — e recebe `hoje` como
  parâmetro (padrão: `diaISO(new Date())`, o dia local). Um dia que não existe (`2026-02-30`) reprova pelo mesmo
  teste da agenda: `somarDias` o devolve como outro dia. **Lança** se a parcela não existe ou já está paga: a tela nem
  oferece a baixa numa parcela paga, e desfazer a baixa é o estorno (item 10.8 do plano da v1).
- **Depois da baixa**: a parcela leva `pagoEm` e `forma` juntos, a pílula vira `Paga`, a linha mostra
  `pago em 15/10/2026 · Pix` e, nas contas a receber, ela vai para o fim da lista e sai do resumo das parcelas em
  aberto. Aparece o aviso `Baixa registrada`.

## Movimento e micro-interações

O modal é o do kit: abre com a animação dele e o foco entra no painel. O botão é o secundário do kit, em pílula.

## Histórico de mudanças

- [[2026-09-30-pr-147-financeiro-baixa]] — o botão **Dar baixa** e o modal da forma e do dia do pagamento; `darBaixa`.
