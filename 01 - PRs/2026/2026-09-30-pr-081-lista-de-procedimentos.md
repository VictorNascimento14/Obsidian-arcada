---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 81
url: https://github.com/VictorNascimento14/Arcada/pull/81
branch: feat/procedimentos-lista
tags: [pr, procedimentos, lista, busca]
status: aberto
---

# PR #81 — feat(procedimentos): listar procedimentos com busca por nome e código e filtro por especialidade

## 🎯 Contexto

Item 6.2 (Lista com busca e filtro) do [[2026-09-30-plano-da-v1]], sobre o catálogo e a semente do PR #74 (item 6.1). O desenho segue a lista de pacientes (PR #51): campo de busca, contagem numa região `status` e estados vazios. Fecha a issue #77.

## 🔧 Mudanças

- `src/modulos/procedimentos/busca.ts` (+ teste): `filtrarProcedimentos` (termo, especialidade e ordem) e `especialidadesDe` (as opções do filtro).
- `src/modulos/procedimentos/ListaProcedimentos.tsx` (+ teste): a tela, com busca, filtro, contagem e os estados vazios.
- `src/modulos/procedimentos/modulo.ts`: a rota `/procedimentos` e o item da coluna (Cadastros, ordem 10, ícone `list`).

## 🧠 Decisões técnicas

- **A busca exige todas as palavras, em qualquer ordem**, e não o texto inteiro como trecho (o desenho de `pacientes/busca.ts`): os nomes têm palavras de ligação e vírgula (`Tratamento de canal, dente birradicular`), e `canal birradicular` tem de achar.
- **A busca olha o nome e o código, não a especialidade**: para a área há o filtro.
- **O filtro lista só as especialidades que a tabela tem**, na ordem do catálogo padrão e, as de fora dele, depois em ordem alfabética: `especialidade` segue texto livre no tipo, e o filtro não pode ignorar o que o cadastro do 6.3 aceitar.
- **Ordem: especialidade (a do catálogo) e nome**, sem seções: cada linha diz a sua especialidade.
- **A especialidade escolhida é derivada.** O estado guarda o que foi escolhido, e a tela só usa o que ainda existe na tabela; se ela some, o filtro vale `Todas` sem `useEffect`.
- **A normalização foi copiada, não importada**: a `chave` de `pacientes/busca.ts` não é exportada, e um módulo não edita arquivo de outro. São uma linha; a extração para um lugar comum fica para o terceiro uso.
- **Sem `Inativo` nem botões por ora**: o cadastro é o item 6.3 e ativar ou desativar, o 6.5.

## ⚠️ Armadilhas e aprendizados

- `formatarReais` separa o `R$` do número com espaço não separável (U+00A0): o teste compara com `formatarReais(...)`, não com texto digitado à mão.
- **Sem verificação visual em navegador**: o Chrome de teste não conecta nesta máquina. Conferi no CSS do build que as classes novas (`md:grid-cols-[1fr_16rem]`, `tabular-nums`) existem; o resto reusa classes de telas já vistas (pacientes e clínica).

## 🧪 Como testar

1. `npm run dev` e abra o item **Procedimentos** (grupo Cadastros): a tabela lista os 32 procedimentos, de Prevenção a Ortodontia, com preço em reais e duração.
2. Digite `restauracao resina`: sobra «Restauração em resina composta» e a contagem mostra `1 de 32 procedimentos`. Digite `cir-01`: acha a exodontia simples pelo código.
3. Escolha uma especialidade no filtro: só as dela aparecem. Com uma busca que não casa, o cartão «Nenhum procedimento encontrado» traz `Limpar filtros`, que zera a busca e a especialidade.
4. `npx vitest run --maxWorkers=2 src/modulos/procedimentos` — a regra da busca e a tela.

## 📎 Documentação afetada

- [[Lista de procedimentos]]
- [[CatalogoPadrao]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
