---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, progresso]
---

# Progresso do tratamento

## O que é

Quanto do plano de tratamento já foi feito: uma barra com os itens realizados sobre o total, e o número ao lado
(`2 de 3 realizados`). Aparece em cada plano da aba `Tratamentos` da [[FichaDoPaciente]] e na lista de
[[PlanosEmAberto]]. A tela do plano ([[PlanoDeTratamento]]) não a mostra.

## Onde está no código

- `src/modulos/tratamentos/progresso.ts` — `progressoDoPlano(plano)`, que devolve `{ feitos, total, pct }`.
- `src/modulos/tratamentos/ProgressoDoPlano.tsx` — o componente: uma `MeterBar` e o texto.
- `AbaTratamentos.tsx` e `PlanosEmAberto.tsx` — onde ele entra, na coluna de texto de cada linha.
- `plano.ts` — `itensRealizados`, a lista sobre a qual a conta é feita ([[TotaisDoPlano]]).

## Comportamento

- **A conta é por item, não por valor**: `feitos` são os itens com `realizadoEm`; `total`, os itens do plano. Um
  item de R$ 10 pesa o mesmo que um de R$ 3.000: o progresso diz quantos procedimentos foram feitos, não quanto do
  orçamento.
- **`pct`** vai de 0 a 100, arredondado para o inteiro mais próximo (`1 de 3` é 33, `2 de 3` é 67, `1 de 8` é 13).
- **Plano sem itens não tem barra**: não há o que medir, e o componente não desenha nada. A conta devolve tudo
  zerado, sem dividir por zero.
- **Todo plano com itens mostra a barra**, seja qual for a situação: um plano proposto marca `0 de N`. Quem grava
  `realizadoEm` é o atendimento ([[TotaisDoPlano]]); enquanto nada o grava, a barra fica em zero.
- **Acessível**: a barra é uma `progressbar` com o rótulo `Progresso do tratamento: 2 de 3 itens realizados` (no
  singular, `0 de 1 item realizado`). O texto ao lado é `aria-hidden`, para o leitor de tela não ouvir a mesma
  coisa duas vezes.
- Barra de 7 px na cor primária do sistema, com largura máxima de `20rem` dentro da linha.

## Movimento e micro-interações

A barra cresce da esquerda quando entra na tela, como toda `MeterBar` do sistema.

## Histórico de mudanças

- [[2026-09-30-pr-108-tratamentos-progresso]] — o progresso do tratamento na aba da ficha e na lista dos planos em aberto.
