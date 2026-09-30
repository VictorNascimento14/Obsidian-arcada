---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 60
url: https://github.com/VictorNascimento14/Arcada/pull/60
branch: feat/tratamentos-situacao
tags: [pr, tratamentos, situacao]
status: aberto
---

# PR #60 — feat(tratamentos): definir as transições da situação do plano

## 🎯 Contexto

Item 7.6 (Situação do plano) do [[2026-09-30-plano-da-v1]], módulo 7. O tipo `SituacaoPlano` já existe em `src/dominio/tratamento.ts`, e o [[glossario]] deixava as transições como `<A DEFINIR>`. A aprovação que gera as parcelas (financeiro 10.1) e as telas do módulo vão consultar esta regra. Fecha a issue #56.

## 🔧 Mudanças

- `src/modulos/tratamentos/situacao.ts` — `proximasSituacoes`, `podeTransitar` e `transitar`.
- `src/modulos/tratamentos/situacao.test.ts` — as 25 combinações de situação, o caminho inteiro e os desvios recusados.

## 🧠 Decisões técnicas

- **Só as transições do backlog existem.** Proposto → aprovado | recusado; aprovado → em andamento; em andamento → concluído. Recusar depois de aprovar, pular etapa, voltar e ficar onde está lançam `RangeError`.
- **`recusado` e `concluido` são o fim; não há reabrir plano na v1.** Se o paciente reconsidera, como registrar isso é decisão de produto — a regra não abre esse caminho por conta própria.
- **A regra não olha os itens nem dispara efeitos.** Ela só diz se o passo existe. Aprovar gerar as parcelas e o primeiro procedimento feito abrir o andamento são de quem chama.
- **Uma tabela (`PROXIMAS`) guia tudo:** `proximasSituacoes` devolve a linha, `podeTransitar` pergunta se `para` está nela e `transitar` só recusa o que `podeTransitar` recusa — uma fonte só.
- **`transitar` recusa o passo inválido mesmo se a tela esquecer de desabilitar o botão.** Habilitar por `proximasSituacoes` é conforto da tela; a garantia está em `transitar`.

## ⚠️ Armadilhas e aprendizados

- Os valores de `SituacaoPlano` são `em-andamento` (com hífen) e `concluido` (sem acento); o rótulo com acento — "Em andamento", "Concluído" — é da tela.

## 🧪 Como testar

1. `npm test` → `situacao.test.ts` passa: das 25 combinações de situação, só proposto → aprovado, proposto → recusado, aprovado → em andamento e em andamento → concluído são permitidas.
2. Ainda em `situacao.test.ts`: `transitar` devolve o plano na situação nova sem mexer no original, percorre o caminho inteiro e lança `RangeError` no passo que não existe (pular etapa, recusar depois de aprovado, voltar, ficar onde está).
3. `npm run lint && npm run type-check && npm run build` passam.

## 📎 Documentação afetada

- [[SituacaoDoPlano]]
- [[TiposDoDominio]]
- [[glossario]]
- [[2026]] (changelog)
