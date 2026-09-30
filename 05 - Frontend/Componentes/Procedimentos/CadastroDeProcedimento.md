---
tipo: funcionalidade
camada: frontend
area: Procedimentos
rota: /procedimentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, procedimentos, cadastro]
---

# Cadastro de procedimento

## O que é

O modal que cadastra e edita um procedimento da tabela, aberto pela tela [[ListaDeProcedimentos]]:
**Novo procedimento** (o botão ao lado da busca) cria; **Editar**, em cada linha, altera o procedimento no
lugar.

## Onde está no código

- `src/modulos/procedimentos/EditorDeProcedimento.tsx` — o modal (`Modal` do kit).
- `src/modulos/procedimentos/cadastro.ts` — `camposDoProcedimento`, `validarProcedimento`,
  `salvarProcedimento`, `especialidadesOferecidas` e `LIMITES_DO_PROCEDIMENTO`.
- `src/modulos/procedimentos/ListaProcedimentos.tsx` — os botões que o abrem.
- Grava na coleção `procedimentos` (`src/dados/colecoes.ts`).

## Comportamento

- **Campos**: Nome (obrigatório, até 100 caracteres); Especialidade (obrigatória, lista fechada: as oito do
  [[CatalogoPadrao]] e as de fora dele que a tabela já tem); Código (opcional, até 20 caracteres, sem repetir o
  de outro procedimento e sem distinguir caixa); Preço (R$); Duração (minutos); e as exigências «Exige dente»
  e «Exige face».
- **Preço**: digitado no padrão brasileiro — `180`, `180,5`, `1.234,56`, `R$ 90,00` — e guardado em centavos
  por `paraCentavos`. O ponto é milhar (`1.300` são R$ 1.300,00); `12,345`, `12.50`, sinal e texto são
  recusados. Preço zero é aceito. Ao editar, o campo abre como se digita (`1.234,56`).
- **Duração**: inteiro de 1 a 480 minutos, só dígitos.
- **Dente e face**: «Exige face» só se marca com «Exige dente», e desmarcar o dente desmarca a face. A função
  de escrita também recusa face sem dente.
- **Novo** nasce ativo e com id novo. **Editar** troca no lugar (mesmo id) e mantém o que o formulário não
  edita: `ativo` e `condicaoResultante` seguem como estavam.
- **Salvar** valida pela função de escrita: com erro, o modal continua aberto, cada campo mostra a mensagem e
  nada é gravado. Cancelar, Esc e clique fora fecham sem gravar. Salvou: o aviso «Procedimento cadastrado»
  ou «Procedimento atualizado» e o modal fecha.
- **Sem exclusão**: o procedimento que já foi orçado ou feito precisa continuar no histórico; a saída é
  desativar (item 6.5 do [[2026-09-30-plano-da-v1]]).

## Movimento e micro-interações

O modal é o do kit: o foco entra nele e volta ao botão que o abriu quando fecha. A especialidade é um
`<select>` nativo, que desdobra pelo CSS do kit no Chrome e no Edge.

## Limites conhecidos

- O formulário não escolhe a condição que o procedimento deixa no odontograma (`condicaoResultante`): o
  editado mantém a que tem, e o novo nasce sem ela.
- Uma especialidade fora do catálogo não se cria pelo formulário; o próximo degrau é uma opção «Outra» com
  campo de texto.
- Sem verificação visual em navegador (o Chrome de teste não conecta nesta máquina).

## Histórico de mudanças

- [[2026-09-30-pr-096-cadastro-de-procedimento]] — O cadastro e a edição, com a validação do preço, da duração e do código.
