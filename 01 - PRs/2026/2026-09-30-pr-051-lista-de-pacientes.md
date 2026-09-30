---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 51
url: https://github.com/VictorNascimento14/Arcada/pull/51
branch: feat/pacientes-lista
tags: [pr, pacientes, lista, busca]
status: aberto
---

# PR #51 — feat(pacientes): listar pacientes com busca por nome e telefone

## 🎯 Contexto

Item 1.1 do [[2026-09-30-plano-da-v1]] (módulo 1 · Pacientes): a porta de entrada do módulo e o primeiro `modulo.ts` dele. O cadastro (1.3) e a ficha (1.5) acrescentam as rotas deles ao mesmo arquivo. Fecha a issue #41.

## 🔧 Mudanças

- `src/modulos/pacientes/modulo.ts` — registra o módulo: a rota `/pacientes` e o item `Pacientes` da coluna (grupo Consultório, ordem 20, ícone `users`, também na barra do celular).
- `src/modulos/pacientes/ListaPacientes.tsx` — a tela: campo de busca, contagem (`role="status"`), cartões que levam a `/pacientes/:id` e os dois estados vazios.
- `src/modulos/pacientes/busca.ts` e `busca.test.ts` — `filtrarPacientes(lista, termo)`: ordena por nome (pt-BR) e filtra por nome e por telefone.
- `src/modulos/pacientes/exibicao.ts` e `exibicao.test.ts` — `anosDoPaciente`, `rotuloIdade` e `rotuloConvenio`: idade e convênio em texto, que a ficha (1.5) reaproveita.
- `src/modulos/pacientes/ListaPacientes.test.tsx` — a tela montada pelas rotas de verdade: o item da coluna leva à lista, a ordem, idade e convênio, busca por nome e por telefone, sem resultado e coleção vazia.

## 🕵️ Dado pessoal (LGPD)

A lista mostra nome, idade, convênio e telefone do paciente — dado pessoal — só na tela: a coleção é local ao navegador (ADR-001) e nada é enviado. Semente e teste usam nomes fictícios e telefones com DDD 00; a lista não exibe nem busca CPF.

## 🧠 Decisões técnicas

- **Busca por telefone só quando o termo é todo de dígitos e máscara.** `ana 9` é um nome; tratado como telefone, acenderia todo paciente com um 9 no número. O `55` do país é tirado de um número completo (mais de 11 dígitos), como em `contato.ts`; `+55` com o número ainda incompleto não casa (55 também é um DDD).
- **Nome por trecho, sem acento e sem caixa**: `normalize("NFD")` e remoção das marcas, com os espaços repetidos colapsados nos dois lados da comparação.
- **Ordenação com `Intl.Collator("pt-BR")` criado uma vez** — `localeCompare` com locale monta um colador por comparação. O acento não muda a posição: `Ângela` fica entre `Ana` e `João`.
- **Filtro e ordem derivados na tela com `useMemo`**, sobre `useColecao(pacientes)` — nunca dentro do hook, que precisa devolver a mesma referência até algo ser gravado.
- **Idade ilegível não derruba a lista**: `anosDoPaciente` devolve `null` para nascimento fora do formato ou no futuro, e o cartão mostra só o convênio. Sem convênio (ausente ou em branco), `Particular`.
- **Cartão é `Link` em volta de `GlassCard interactive`**, com o raio de 26px do vidro no link para o anel de foco acompanhar o cartão. Sem paginação: a demo tem dezenas de pacientes (comentário `ponytail:` na tela nomeia o teto).

## ⚠️ Armadilhas e aprendizados

- O `Avatar` já tem `role="img"` e `aria-label="Iniciais de …"`; dentro do link o leitor de tela leria as iniciais e depois o nome. Por isso o `<span aria-hidden>` em volta.
- Os itens da coluna e da barra do celular são `<button>`, não `<a>`: o teste os acha por `role="button"`.
- `vi.useFakeTimers({ toFake: ["Date"] })` fixa o dia do teste (a idade sai certa) sem parar os temporizadores que o `findBy` da Testing Library usa.

## 🧪 Como testar

1. `npm run dev` e abra `/pacientes` (ou o item Pacientes da coluna lateral): aparecem os 8 pacientes de demonstração em ordem alfabética, cada um com idade, convênio e telefone, e a contagem `8 pacientes`.
2. Digite `joao` no campo: só `João Pedro Alves` fica, e a contagem vira `1 de 8 pacientes`. Digite `luisa` (sem acento): acha `Luísa Fernandes`.
3. Digite `0002` ou `90000-0002`: acha a paciente do telefone `(00) 90000-0002`. Digite `zzz`: aparece `Nenhum paciente encontrado`, e `Limpar busca` traz a lista de volta.
4. Estreite a janela (ou abra no celular): os cartões passam a uma coluna e o item `Pacientes` aparece na barra de baixo.
5. `npm test -- src/modulos/pacientes` roda `busca.test.ts`, `exibicao.test.ts` e `ListaPacientes.test.tsx`; `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[Lista de pacientes]]
- [[IdadeEFaixaEtaria]]
- [[2026]] (changelog)
