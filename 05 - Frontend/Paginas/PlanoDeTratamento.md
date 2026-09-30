---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: /planos/:planoId
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, plano, orcamento, fdi]
---

# Plano de tratamento

## O que é

A tela de um plano de tratamento: os procedimentos propostos a um paciente, cada um no dente e nas faces em que
será feito, e o orçamento deles (subtotal, desconto e total) com a situação do plano. Chega-se a ela pela aba
`Tratamentos` da [[FichaDoPaciente]], que lista os planos do paciente e cria um novo.

## Onde está no código

- `src/modulos/tratamentos/modulo.ts` — a rota `/planos/:planoId` e a aba `Tratamentos` da ficha (`abaPaciente`,
  ordem 40).
- `TelaDoPlano.tsx` — a tela: o cartão dos itens e, abaixo, o do orçamento.
- `FormularioDoItem.tsx` — o modal de novo item (procedimento, dente, faces e preço).
- `OrcamentoDoPlano.tsx` — subtotal, desconto, total, o campo do desconto e os botões da situação.
- `AbaTratamentos.tsx` — a aba da ficha; `SituacaoBadge.tsx` — a pílula da situação.
- `itens.ts` — a regra do item: procedimentos que se pode escolher, dentes do `<select>`, campos do formulário e
  `itemDoFormulario`, que valida e converte.
- `planos.ts` — as escritas: `criarPlano`, `adicionarItem`, `removerItem`, `definirDesconto`, `mudarSituacao`.
- `exibicao.ts` — rótulos da situação e dos botões, e `detalheDoItem`.
- As regras que já existiam: [[TotaisDoPlano]], [[DescontoDoOrcamento]] (`desconto.ts` ganha `lerDesconto`, que lê
  o campo) e [[SituacaoDoPlano]].

## Comportamento

### Aba `Tratamentos` da ficha

- Lista os planos do paciente, na ordem em que foram criados: `Plano 1`, `Plano 2`…, com o número de itens, a
  situação e o total (já com o desconto). Cada linha abre `/planos/:planoId`.
- `Novo plano` cria um plano `Proposto`, sem itens e sem desconto, e abre a tela dele. O plano não tem campo a
  preencher (nem nome nem data), por isso não existe uma tela de "novo plano" antes da tela do plano.

### Adicionar item

`Adicionar item`, só com o plano proposto, abre o formulário:

- **Procedimento**: lista só com os **ativos** do catálogo, agrupada por especialidade e em ordem alfabética
  (`DEN-01 · Restauração em resina composta`). Procedimento desativado continua nos itens que já entraram num plano,
  mas não aparece para itens novos.
- **Dente**: só quando o procedimento exige dente. Um `<select>` com os 32 permanentes (11 a 48) e os 20 decíduos
  (51 a 85), na ordem dos números, com o nome: `16 — primeiro molar superior direito` ([[NotacaoFdi]]).
- **Faces**: só quando o procedimento exige face. Caixas de seleção só com as faces do dente escolhido
  ([[FacesDoDente]]): vestibular, mesial e distal em todos; palatina nos superiores e lingual nos inferiores;
  incisal nos da frente e oclusal nos de trás. Enquanto não há dente, o campo diz `Escolha o dente para ver as
  faces dele.` Trocar de dente desmarca a face que o novo dente não tem.
- **Preço**: vem preenchido com o da tabela (`220,00`) e pode ser ajustado. Escolher outro procedimento traz o preço
  do novo. Zero vale (cortesia). O item guarda o preço digitado, em centavos: reajustar o catálogo depois não muda
  o que já foi orçado.
- O item guarda as faces na ordem do dente (V, M, D, P ou L, O ou I), qualquer que seja a ordem dos cliques.
- Erros por campo, sem fechar o modal: `Escolha o procedimento.`, `Escolha o dente.`, `Marque ao menos uma face.`,
  `Informe o preço, como 220,00.`

O item aparece na lista como `Dente 16 · primeiro molar superior direito · faces mesial, oclusal`, com o preço, e
`Remover` o tira (enquanto o plano é proposto). O item não se edita: remove-se e adiciona-se de novo.

### Orçamento e situação

- **Subtotal**, **Desconto** e **Total**. A linha do desconto mostra a diferença que vale (`subtotal − total`).
- **Desconto** (só proposto): `Percentual (%)` ou `Valor (R$)`. O percentual aceita até duas casas (`12,5`; o
  ponto também vale) e arredonda para o centavo mais próximo; o valor segue o padrão de `paraCentavos`. Nunca passa
  do subtotal, e aplicar de novo substitui o anterior. Zero tira o desconto.
- **Situação**: os botões são os passos de `proximasSituacoes`: `Aprovar` e `Recusar` no proposto; `Iniciar
  tratamento` no aprovado; `Concluir` no em andamento. `Aprovar` fica desabilitado enquanto não há item.
- **Plano aprovado** (e os seguintes) **não muda de itens nem de desconto**: os botões somem e a tela avisa. Aprovado,
  o plano gera as parcelas do financeiro; mexer no orçamento depois desalinharia o orçado do cobrado.
- As transições não têm volta na v1, e não há confirmação antes de `Recusar` ou de `Concluir`.

### Plano que não existe

`/planos/qualquer-coisa` mostra `Plano não encontrado` e o link `Voltar à lista de pacientes`.

## Movimento e micro-interações

Cartões de vidro; o formulário abre em modal (foco entra no painel e volta ao botão ao fechar, Escape fecha). O
`<select>` desdobra animado no Chrome e no Edge. A situação é uma pílula colorida, mas quem diz é o texto.

## Histórico de mudanças

- [[2026-09-30-pr-097-tratamentos-itens]] — a tela do plano com itens por dente e face, o desconto e a situação, e a aba `Tratamentos` da ficha.
