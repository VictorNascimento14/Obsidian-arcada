---
tipo: funcionalidade
camada: frontend
area: Procedimentos
rota: /procedimentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, procedimentos, ativo, fluxo]
---

# Ativar e desativar procedimento

## O que é

Como a clínica tira um procedimento das escolhas sem apagá-lo: desativa-se o procedimento, que **continua na tabela
e no histórico** mas some do que se oferece para itens novos. Reativar volta tudo ao que era. O fluxo atravessa três
módulos: Procedimentos muda o `ativo`; o plano de tratamento e a agenda o leem.

## Onde está no código

- **Muda e mostra** (módulo Procedimentos): `src/modulos/procedimentos/EditorDeProcedimento.tsx` (a caixa
  «Procedimento ativo» do modal, ver [[CadastroDeProcedimento]]), `cadastro.ts` (`camposDoProcedimento` e
  `salvarProcedimento` levam o `ativo`) e `ListaProcedimentos.tsx` (a marca «Inativo», ver
  [[ListaDeProcedimentos]]).
- **Lê para escolher** (só os ativos): `src/modulos/tratamentos/itens.ts` (`procedimentosParaEscolher` e
  `itemDoFormulario`) e `src/modulos/agenda/MarcarConsulta.tsx` e `marcar.ts` (a lista da escolha e o erro «Escolha
  um procedimento ativo.»).
- **Lê para exibir o histórico** (a tabela inteira, sem filtrar): `src/modulos/tratamentos/TelaDoPlano.tsx`,
  `src/modulos/agenda/GradeDoDia.tsx` e `DetalheDaConsulta.tsx` buscam o procedimento por `id`.
- Tipo: `Procedimento.ativo`, em `src/dominio/procedimento.ts`.

## Comportamento

- **Desativar**: em `/procedimentos`, Editar, desmarcar «Procedimento ativo» e Salvar. A linha ganha a marca
  «Inativo» e continua na lista, na busca e no filtro por especialidade.
- **Reativar**: o mesmo caminho, marcando a caixa.
- **O que some**: o procedimento inativo deixa de aparecer na escolha do item do plano e na da consulta, e as duas
  regras de gravação recusam um `procedimentoId` inativo — `itemDoFormulario` («Escolha o procedimento.») e
  `marcar.ts` («Escolha um procedimento ativo.»).
- **O que fica**: planos e consultas que já usavam o procedimento continuam mostrando o nome dele, porque a busca por
  `id` não filtra por `ativo`; o item do plano também guarda a própria cópia do preço.
- **Procedimento novo** nasce ativo (`camposDoProcedimento()` sem argumento) e só se desativa depois.
- **Reajuste em lote**: o procedimento inativo, quando selecionado, também é reajustado.
- **Regra para quem consumir a tabela**: para *escolher* um procedimento, filtre por `ativo`; para *exibir* um que já
  foi escolhido, busque por `id` na tabela inteira. Um módulo novo que liste sem filtrar reabre o inativo para itens
  novos.

## Movimento e micro-interações

Nenhuma além das do modal de edição e da lista.

## Histórico de mudanças

- [[2026-09-30-pr-129-ativar-e-desativar-procedimento]] — a caixa «Procedimento ativo» no modal e a marca «Inativo» na lista.
