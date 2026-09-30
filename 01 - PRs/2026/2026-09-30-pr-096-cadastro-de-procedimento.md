---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 96
url: https://github.com/VictorNascimento14/Arcada/pull/96
branch: feat/procedimentos-cadastro
tags: [pr, procedimentos, cadastro]
status: merged
---

# PR #96 — feat(procedimentos): cadastrar e editar procedimento com preço, duração e exigência de dente e face

## 🎯 Contexto

Item 6.3 (Cadastro e edição) do [[2026-09-30-plano-da-v1]], sobre a lista do PR #81 (6.2) e o catálogo do PR #74 (6.1). O desenho de lista e modal repete o de profissionais (PR #64) e o de cadeiras (PR #69), da tela Clínica. Fecha a issue #87.

## 🔧 Mudanças

- `src/modulos/procedimentos/cadastro.ts` (+ teste): `camposDoProcedimento`, `validarProcedimento`, `salvarProcedimento` e `especialidadesOferecidas`, com `LIMITES_DO_PROCEDIMENTO`.
- `src/modulos/procedimentos/EditorDeProcedimento.tsx` (+ teste): o modal de cadastro e edição.
- `src/modulos/procedimentos/ListaProcedimentos.tsx` (+ testes): o botão «Novo procedimento», o «Editar» de cada linha e o modal. A faixa de busca e filtro passa a quebrar linha (`flex-wrap`) em vez de grade, para o botão caber ao lado; a linha da lista empilha no celular.

## 🧠 Decisões técnicas

- **A regra que valida e grava é pura e mora em `cadastro.ts`**, como a de profissionais e a de cadeiras: `salvarProcedimento` valida (é ela a regra; a tela só mostra o erro) e só então grava. O que o formulário não edita (`ativo`, `condicaoResultante`) segue como estava, porque a gravação parte do registro guardado.
- **Preço é texto no formulário e centavos na coleção**: `paraCentavos` na entrada e, ao abrir a edição, `formatarReais` sem o `R$`. Ninguém divide por 100 no meio do caminho. Preço zero é aceito (cortesia, retorno sem custo).
- **Duração de 1 a 480 minutos, só em dígitos.** O teto de 8 horas é sanidade de uma agenda de um dia. O campo é texto com teclado numérico, sem o giro do `type=number`.
- **Código opcional e único**, sem distinguir caixa: é o que a busca da lista e a impressão usam para identificar o procedimento. Nome repetido é aceito, como em cadeiras.
- **Especialidade é lista fechada** (as oito do catálogo, mais as de fora que a tabela já tem), num `<select>`: um campo livre criaria `endodontia` ao lado de `Endodontia` por engano de digitação, e dropdown novo tem de ser `<select>` ou `<Dropdown>`. O teto: para uma área nova, o próximo degrau é uma opção `Outra` com texto.
- **Exigir a face exige o dente, nas duas pontas**: a tela desabilita a face sem o dente e a desmarca ao desmarcar o dente; a função de escrita recusa a combinação.
- **Sem exclusão e sem ativo/inativo ainda**: o procedimento que já foi orçado ou feito precisa continuar no histórico, e ativar ou desativar é o item 6.5. Enquanto isso, o novo nasce ativo.
- **O formulário não edita `condicaoResultante`**: o procedimento editado mantém a que tem, e o novo nasce sem. Escolher a condição no cadastro fica para um item próprio.

## ⚠️ Armadilhas e aprendizados

- **O ponto é milhar, não decimal**: `1.300` são R$ 1.300,00 (130000 centavos), e `12.50` é recusado. É a regra do `paraCentavos`; o campo mostra a dica `Ex.: 180,00`.
- `formatarReais` põe um espaço não separável (U+00A0) depois do `R$`: a expressão que tira o prefixo do campo usa `\s`, que o cobre.
- `Number("1e2")` vale 100: por isso a duração passa por uma expressão de só dígitos antes de virar número.
- **Sem verificação visual em navegador** (o Chrome de teste não conecta nesta máquina). Conferi no CSS do build que as classes novas existem (`min-w-[14rem]`, `sm:w-56`, `sm:ml-auto`, `sm:flex-row`, `sm:justify-end`…); o modal usa o `Modal` do kit e os mesmos campos do cadastro de profissionais.

## 🧪 Como testar

1. `npm run dev`, abra **Procedimentos** e use **Novo procedimento**: preencha nome, especialidade, preço (`95,90`) e duração (`20`) e salve. Ele entra na lista, na sua especialidade, com `R$ 95,90` e `20 min`.
2. Salve o formulário vazio: cada campo obrigatório mostra o erro, o modal continua aberto e nada é gravado. Preço `12,345`, duração `500` e um código já usado por outro procedimento também são recusados.
3. Em **Editar**, numa linha do catálogo, mude o preço (o campo abre como se digita, por exemplo `220,00`) e salve: a linha mostra o valor novo, no mesmo lugar da lista.
4. Sem marcar **Exige dente**, **Exige face** está desabilitado. Marque o dente e a face, depois desmarque o dente: a face desmarca junto.
5. `npx vitest run --maxWorkers=2 src/modulos/procedimentos` — a regra do cadastro, o modal e a lista.

## 📎 Documentação afetada

- [[CadastroDeProcedimento]]
- [[ListaDeProcedimentos]]
- [[CatalogoPadrao]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
