---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 182
url: https://github.com/VictorNascimento14/Arcada/pull/182
branch: feat/painel-em-aberto
tags: [pr, painel, tratamentos, orcamento]
status: aberto
---

# PR #182 — feat(painel): mostrar a contagem e o valor dos orçamentos e tratamentos em aberto

## 🎯 Contexto

Item 13.3 do [[2026-09-30-plano-da-v1]] (módulo 13 · Painel): contagem e valor dos tratamentos e orçamentos em aberto, sobre a mesma tela do 13.1 ([[ConsultasDeHoje]]) e do 13.2 ([[IndicadoresDoMes]]). Reaproveita `planosEmAberto` ([[PlanosEmAberto]]) e `total` ([[TotaisDoPlano]]) do módulo de tratamentos, sem editá-los. Fecha a issue #180.

## 🔧 Mudanças

- `src/modulos/painel/resumoEmAberto.ts` — `resumoEmAberto(planos, pacientes)`: a contagem e o valor dos orçamentos e dos tratamentos em aberto.
- `src/modulos/painel/resumoEmAberto.test.ts` — 5 testes: a separação entre orçamentos e tratamentos com o desconto, o concluído e o recusado fora, o desconto maior que o subtotal, o plano de paciente removido e o vazio.
- `src/modulos/painel/TratamentosEmAberto.tsx` — o bloco: título `Planos em aberto`, o link `Ver os planos` e uma lista com dois `StatCard`.
- `src/modulos/painel/TratamentosEmAberto.test.tsx` — 3 testes de tela: a contagem e o valor de cada grupo, o link e o estado sem plano em aberto.
- `src/modulos/painel/Painel.tsx` — monta o bloco depois dos indicadores do mês.

## 🧠 Decisões técnicas

- **Em aberto é o que `planosEmAberto` diz**: proposto, aprovado e em andamento. A regra vive no módulo de tratamentos e o painel só a chama, com os pacientes, como a lista `/tratamentos`: os dois lugares mostram a mesma contagem.
- **Dois grupos que repartem os planos em aberto**: orçamento é o proposto (a proposta de valores que espera o paciente, pelo glossário) e tratamento é o aprovado ou em andamento. O que não é proposto cai em tratamentos por construção, então a soma das duas contagens é sempre a dos planos em aberto.
- **O valor é a soma dos `total` dos planos**, já com o desconto, em centavos: o que o paciente paga, como no resumo da lista `/tratamentos`. Não é o que falta fazer: isso pediria uma regra de valor restante por item que o plano não define.
- **A contagem é o número grande e o valor vai no rodapé**, em texto por `formatarReais`: o `AnimatedNumber` anima a contagem, e o dinheiro fica estável, sem contar de zero.
- **Plano de paciente que já não existe continua contando**, como na lista: o plano existe e tem valor.
- **Sem plano em aberto**, cada cartão mostra 0 e diz `Nenhum orçamento à espera do paciente` ou `Nenhum tratamento aprovado ou em andamento`: a demonstração nasce sem planos e o painel não fica mudo.
- **O link `Ver os planos`** leva a `/tratamentos`, a lista que detalha o que o painel resume.

## ⚠️ Armadilhas e aprendizados

- Partir os grupos por `situacao !== "proposto"` parece frouxo, mas é o que garante que eles somem o total em aberto: listar `aprovado` e `em-andamento` à mão esqueceria um estado novo que `SITUACOES_EM_ABERTO` ganhasse.
- O teste de tela acha os cartões por `listitem` e lê o `sr-only` do `AnimatedNumber` (estável), e não o texto que anima.

## 🧪 Como testar

1. `npx vitest run src/modulos/painel --maxWorkers=2`: 35 testes, 8 deles novos. Cinco da regra (a separação, o concluído e o recusado fora, o desconto maior que o subtotal, o paciente removido e o vazio) e três da tela (contagem e valor, o link e o zero).
2. `npm run dev` e abrir `/`: a demonstração nasce sem planos, então os dois cartões mostram 0 e dizem que não há nenhum. Crie um plano na aba Tratamentos da ficha de um paciente: enquanto é proposto, conta como orçamento em aberto, com o valor; aprovado, passa a tratamento.
3. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[PainelDoConsultorio]]
- [[TratamentosEmAberto]]
- [[2026]] (changelog)
