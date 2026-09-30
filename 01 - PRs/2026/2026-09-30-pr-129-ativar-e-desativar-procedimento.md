---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 129
url: https://github.com/VictorNascimento14/Arcada/pull/129
branch: feat/procedimentos-ativar
tags: [pr, procedimentos, ativo]
status: merged
---

# PR #129 — feat(procedimentos): ativar e desativar o procedimento

## 🎯 Contexto

Item 6.5 (Ativar e desativar) do [[2026-09-30-plano-da-v1]], sobre o cadastro e a edição (PR #96), a lista (PR #81) e o reajuste em lote (PR #121). `Procedimento.ativo` existe desde o tipo do núcleo, e o plano (`tratamentos/itens.ts`) e a agenda (`agenda/marcar.ts` e `MarcarConsulta.tsx`) já só oferecem os ativos. Fecha a issue #126.

## 🔧 Mudanças

- `src/modulos/procedimentos/cadastro.ts` (+ teste): `CamposDoProcedimento` ganha `ativo`; `camposDoProcedimento` o devolve (o novo é ativo) e `salvarProcedimento` o grava.
- `src/modulos/procedimentos/EditorDeProcedimento.tsx` (+ teste): a caixa «Procedimento ativo».
- `src/modulos/procedimentos/ListaProcedimentos.tsx` (+ teste): a marca «Inativo» na linha.

## 🧠 Decisões técnicas

- **A caixa fica no modal de edição, e a linha só ganha a marca.** É o desenho que `Cadeira.ativa` e `Profissional.ativo` já têm; a linha, com preço, duração e «Editar», não comporta outro botão no celular. O caminho é Editar, desmarcar, Salvar.
- **`ativo` virou campo do formulário**, e não mais um dado que a gravação preservava: `salvarProcedimento` grava `campos.ativo`, e `camposDoProcedimento` o traz do procedimento (ou `true`, para o novo). Salvar sem tocar na caixa devolve o que estava.
- **Nada muda em quem lê `ativo`.** Conferido no código: `procedimentosParaEscolher` e `itemDoFormulario` (plano de tratamento), `MarcarConsulta.tsx` e a validação de `marcar.ts` (agenda) já ignoram o inativo.
- **Inativo continua no histórico.** As telas que mostram um procedimento já escolhido (plano, grade do dia e detalhe da consulta) o buscam por `id` na tabela inteira, sem filtrar por `ativo`.
- **O inativo fica na lista, marcado e na mesma ordem.** Some das escolhas, não da tabela: precisa continuar à vista para ser reativado. Não há filtro «só ativos», que ninguém pediu.

## ⚠️ Armadilhas e aprendizados

- Quem consumir a tabela de procedimentos: para **escolher**, filtre por `ativo`; para **exibir** o que já foi escolhido, busque por `id` na tabela inteira. Um módulo novo que liste sem filtrar reabre o inativo para itens novos.
- O reajuste em lote alcança o procedimento inativo, quando ele é selecionado.
- Sem verificação visual (o navegador de teste não conecta nesta máquina): a marca «Inativo» usa as mesmas classes da marca «Inativa» das cadeiras, e a caixa, as do modal.

## 🧪 Como testar

1. `npm run dev`, abra `/procedimentos` e clique em «Editar» num procedimento: o modal traz «Procedimento ativo» marcada.
2. Desmarque a caixa e salve: a linha ganha a marca «Inativo» e continua na lista, na busca e no filtro por especialidade.
3. Ao acrescentar um item ao plano de tratamento de um paciente, e em «Marcar consulta», o procedimento desativado não aparece na escolha de procedimento.
4. Voltando a «Editar» e marcando a caixa de novo, a marca some e o procedimento volta às escolhas.
5. `npx vitest run --maxWorkers=2 src/modulos/procedimentos` — regra, modal e lista.

## 📎 Documentação afetada

- [[Ativar e desativar procedimento]]
- [[CadastroDeProcedimento]]
- [[ListaDeProcedimentos]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
