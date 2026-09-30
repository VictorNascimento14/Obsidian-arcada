---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 97
url: https://github.com/VictorNascimento14/Arcada/pull/97
branch: feat/tratamentos-itens
tags: [pr, tratamentos, plano, itens, fdi]
status: aberto
---

# PR #97 — feat(tratamentos): adicionar itens ao plano por dente e face

## 🎯 Contexto

Item 7.3 do [[2026-09-30-plano-da-v1]] (módulo 7 · Plano de tratamento e orçamento): itens por dente e face. Usa `subtotal`/`total` (7.1), `aplicarDesconto` (7.4) e `transitar` (7.6), o catálogo de procedimentos (6.1, com `exigeDente` e `exigeFace`) e a notação FDI (`facesDoDente`, `nomeDente`, 3.1 e 3.2); encaixa-se na ficha do paciente (1.5) pelo `abaPaciente`. Fecha a issue #85.

## 🔧 Mudanças

- `src/modulos/tratamentos/itens.ts` — a regra do item: `procedimentosParaEscolher` (só os ativos, por especialidade), `DENTES_PARA_ESCOLHER`, os campos do formulário (`escolherProcedimento`, `escolherDente`, `alternarFace`) e `itemDoFormulario`, que valida contra o catálogo e a FDI e devolve o item ou os erros por campo. Com teste.
- `src/modulos/tratamentos/planos.ts` — as escritas do plano: `criarPlano`, `adicionarItem`, `removerItem`, `definirDesconto` e `mudarSituacao`. Itens e desconto só mudam com o plano proposto; aprovar exige ao menos um item. Com teste.
- `src/modulos/tratamentos/exibicao.ts` — rótulos da situação e dos botões, `rotuloItens` e `detalheDoItem`. Com teste.
- `src/modulos/tratamentos/desconto.ts` — `lerDesconto`: o texto do campo vira `Desconto` (percentual com até duas casas, ponto ou vírgula; valor por `paraCentavos`). Teste no `desconto.test.ts`.
- `src/modulos/tratamentos/TelaDoPlano.tsx`, `FormularioDoItem.tsx`, `OrcamentoDoPlano.tsx` — a tela `/planos/:planoId`: itens, o modal de novo item, o orçamento com o desconto e os botões de situação. Testes de tela em `TelaDoPlano.test.tsx`.
- `src/modulos/tratamentos/AbaTratamentos.tsx` e `SituacaoBadge.tsx` — a aba da ficha (lista os planos e cria um novo) e a pílula da situação. Teste em `AbaTratamentos.test.tsx`.
- `src/modulos/tratamentos/modulo.ts` — a rota `/planos/:planoId` e a aba `Tratamentos` (ordem 40); `modulo.test.ts` confere o registro em `NAVEGACAO`.

## 🧠 Decisões técnicas

- **Plano só muda de itens e de desconto enquanto está proposto.** Aprovado, ele gera as parcelas do financeiro (glossário: Orçamento); mexer nos itens depois desalinharia o orçado do cobrado. A regra está em `planos.ts` (as escritas lançam), não só nos botões que somem.
- **A validação mora em `itemDoFormulario`, chamada por `adicionarItem`**: a tela só mostra os erros que a regra devolve, o mesmo desenho de `salvarCadeira`. `exigeFace` implica dente (como diz o tipo `Procedimento`), e procedimento que não pede dente ignora o dente que sobrou no formulário.
- **O preço parte da tabela, mas quem manda é o campo.** Trocar de procedimento traz o preço do novo; o item guarda o valor digitado, em centavos (ADR-005). Zero vale, como cortesia.
- **As faces saem na ordem do dente** (V, M, D, P ou L, O ou I), qualquer que seja a ordem dos cliques: o dado guardado é estável e o teste não depende do usuário. Trocar de dente tira as faces que o novo não tem (`escolherDente`).
- **Não há `/pacientes/:id/planos/novo`**: o plano não tem campo a preencher (nem nome nem data), então `Novo plano` cria e abre a tela do plano. A rota da tela é `/planos/:planoId`.
- **O item da coluna e `/tratamentos` ficam para o item 7.9**, com a lista dos planos em aberto. Registrar a coluna agora deixaria na `main` um link para a página inexistente até o 7.9 entrar.
- **A linha `Desconto` mostra `subtotal − total`**, o que de fato vale: o desconto guardado pode sobrar maior que o subtotal depois que um item sai, e `total` já o limita (item 7.1).
- **Item não se edita, só se remove e se adiciona de novo** (`ponytail:` em `planos.ts`); e não há confirmação antes de `Recusar`/`Concluir`, que não têm volta na v1 (`ponytail:` em `OrcamentoDoPlano.tsx`).

## ⚠️ Armadilhas e aprendizados

- **`getByText` normaliza o espaço não separável do Intl, o texto esperado não**: `getByText(formatarReais(x))` falha mesmo com o texto igual na tela. Nos testes, compare por `textContent` ou normalize com `.replace(/\s/g, " ")` (ver [[2026-09-30-intl-separa-o-real-com-espaco-nao-separavel]]).
- **`nomeDente` lança para um número que a FDI não tem**, e o plano vem do `localStorage`: `detalheDoItem` confere `denteValido` antes, para dado mexido à mão não derrubar a tela do plano.
- **O modal só existe montado enquanto aberto**: com ele sempre montado, a segunda abertura reapareceria com o procedimento, o dente e o preço da primeira.
- **A escolha do dente é `<select>` nativo**, com a receita de campo do `TextField` copiada da tela da clínica: o kit não tem `Select`. O `::picker(select)` só anima no Chrome e no Edge.

## 🧪 Como testar

1. `npm run dev`, abra um paciente em `/pacientes` e a aba `Tratamentos`: aparece `Nenhum plano de tratamento ainda.`
2. `Novo plano`: abre `/planos/<id>` com o plano `Proposto` e sem itens; `Aprovar` está desabilitado, com o motivo.
3. `Adicionar item` e escolha `DEN-01 · Restauração em resina composta`: o preço vem `220,00` e o campo `Dente` aparece. Escolha o dente 16: surgem as faces V, M, D, P e O (no 36, L no lugar de P; no 11, I no lugar de O). Marque M e O, mude o preço para `200,00` e `Adicionar ao plano`: o item aparece como `Dente 16 · primeiro molar superior direito · faces mesial, oclusal` e o total vira R$ 200,00.
4. Escolha `Profilaxia (limpeza)`: não pede dente nem face. Escolha um tratamento de canal: pede o dente, não as faces. Envie o formulário vazio: cada campo mostra o seu erro e nada é gravado.
5. Aplique desconto de 10% e depois de `50,00` em valor: o total abate a cada vez; zero tira o desconto.
6. `Aprovar`: a situação vira `Aprovado`, somem `Adicionar item`, `Remover` e o campo de desconto, e o botão passa a `Iniciar tratamento`. Volte à ficha: a aba mostra o plano com a situação e o total.
7. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam.

## 📎 Documentação afetada

- [[Plano de tratamento]]
- [[Ficha do paciente]]
- [[TotaisDoPlano]]
- [[DescontoDoOrcamento]]
- [[SituacaoDoPlano]]
- [[FacesDoDente]]
- [[NotacaoFdi]]
- [[CatalogoPadrao]]
- [[2026]] (changelog)
