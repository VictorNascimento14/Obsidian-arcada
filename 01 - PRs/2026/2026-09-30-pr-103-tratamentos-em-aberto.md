---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 103
url: https://github.com/VictorNascimento14/Arcada/pull/103
branch: feat/tratamentos-em-aberto
tags: [pr, tratamentos, plano, lista, coluna]
status: aberto
---

# PR #103 — feat(tratamentos): listar os planos de tratamento em aberto

## 🎯 Contexto

Item 7.9 do [[2026-09-30-plano-da-v1]] (módulo 7 · Plano de tratamento e orçamento): a lista dos planos em aberto. Lê as coleções `planos` e `pacientes`, usa `total` (7.1) e as transições de `situacao.ts` (7.6) para definir o que é "em aberto", e abre a tela do plano do 7.3 (#97). Fecha a issue #99.

## 🔧 Mudanças

- `src/modulos/tratamentos/emAberto.ts` — `SITUACOES_EM_ABERTO` e `planosEmAberto`: filtra os planos que ainda têm próximo passo, junta o paciente de cada um e ordena (propostos primeiro; depois por nome). Com teste.
- `src/modulos/tratamentos/PlanosEmAberto.tsx` — a tela `/tratamentos`: as linhas com avatar, paciente, itens, situação e total, o resumo e o estado vazio. Teste de tela em `PlanosEmAberto.test.tsx`.
- `src/modulos/tratamentos/modulo.ts` — a rota `/tratamentos` e o item da coluna (grupo `gestao`, ordem 10, ícone `receipt`); `modulo.test.ts` confere os dois no registro.

## 🧠 Decisões técnicas

- **"Em aberto" inclui o plano aprovado, além do proposto e do em andamento.** O plano aprovado que ainda não teve o primeiro procedimento feito ficaria fora da lista e não haveria outra tela para achá-lo além da aba de cada paciente. A definição é uma constante (`SITUACOES_EM_ABERTO`) e um teste a amarra às situações que ainda têm próximo passo em `situacao.ts`: tirar o aprovado é apagar um elemento dela.
- **Propostos primeiro**, porque são os que esperam uma resposta; em cada grupo, por nome do paciente, comparado em pt-BR (`Ágata` antes de `Ana`). No mesmo paciente e situação vale a ordem de criação (o `sort` é estável).
- **O resumo soma os totais já com desconto** dos planos que estão na lista, não os subtotais.
- **Plano de paciente que já não existe entra na lista**, como `Paciente não encontrado`, e não some: o dado vem do `localStorage` e esconder o plano esconderia o problema. Vem à frente do grupo (nome vazio ordena antes).
- **O item da coluna não entra na barra de baixo do celular** (`barraCelular` fica de fora): o módulo pede só o item da coluna.

## ⚠️ Armadilhas e aprendizados

- **A coluna marca o item pelo prefixo do caminho** (`itemAtivo`): `/planos/:planoId` não fica sob `/tratamentos`, então, na tela do plano, nenhum item da coluna aparece marcado. Mover a rota do plano para `/tratamentos/:planoId` resolveria; não foi feito para não mudar o endereço que o 7.3 já publicou.
- **Ordenar nome com acento pede o locale**: sem `"pt-BR"` no `localeCompare`, a ordem de `Ágata` e `Ana` depende do ambiente. O teste cobre o caso.

## 🧪 Como testar

1. `npm run dev`: o item **Tratamentos** aparece na coluna, no grupo Gestão, e abre `/tratamentos` com `Nenhum plano em aberto` e o botão `Ir para os pacientes`.
2. Em `/pacientes`, abra dois pacientes e, na aba `Tratamentos` de cada um, crie um plano e adicione um item (a profilaxia não pede dente).
3. Volte a **Tratamentos**: os dois planos aparecem com o paciente, `1 item`, `Proposto` e o total, e o resumo diz `2 planos, somando R$ …`. Clique numa linha: abre a tela do plano.
4. Aprove um dos planos: na lista ele passa para depois dos propostos, com `Aprovado`. Recuse o outro: ele some da lista.
5. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam.

## 📎 Documentação afetada

- [[PlanosEmAberto]]
- [[PlanoDeTratamento]]
- [[SituacaoDoPlano]]
- [[TotaisDoPlano]]
- [[2026]] (changelog)
