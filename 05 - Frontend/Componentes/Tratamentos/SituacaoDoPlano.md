---
tipo: funcionalidade
camada: frontend
area: Tratamentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, tratamentos, situacao]
---

# Situação do plano

## O que é

O caminho que um plano de tratamento percorre: proposto, aprovado, em andamento e concluído — ou
recusado, se o paciente não aceita. Não tem tela: as telas do módulo e a aprovação que gera as parcelas
consultam esta regra para saber que passo existe.

## Onde está no código

- `src/modulos/tratamentos/situacao.ts` — `proximasSituacoes`, `podeTransitar` e `transitar`.
- `src/modulos/tratamentos/situacao.test.ts` — os testes.
- `src/dominio/tratamento.ts` — o tipo `SituacaoPlano`, com os cinco valores.

## Comportamento

| De | Para |
|---|---|
| `proposto` | `aprovado` ou `recusado` |
| `aprovado` | `em-andamento` |
| `em-andamento` | `concluido` |
| `concluido` | — (fim) |
| `recusado` | — (fim) |

- **`proximasSituacoes(situacao)`**: para onde um plano naquela situação pode ir. Vazio nas duas finais.
- **`podeTransitar(de, para)`**: se o passo existe. Ficar onde está não é transição. Das 25 combinações,
  só as quatro da tabela são permitidas.
- **`transitar(plano, para)`**: devolve o plano na nova situação, sem mexer no original. Lança
  `RangeError` se o passo não existe — pular etapa, recusar depois de aprovado, voltar. Vale mesmo que a
  tela esqueça de desabilitar o botão.
- **`recusado` e `concluido` são o fim**: não há reabrir plano na v1.
- **A regra não olha os itens nem dispara efeitos.** Aprovar gerar as parcelas, e o primeiro
  procedimento feito abrir o andamento, são de quem chama.
- Os valores são `em-andamento` (com hífen) e `concluido` (sem acento); o rótulo com acento é da tela.

## Histórico de mudanças

- [[2026-09-30-pr-060-tratamentos-situacao]] — as transições da situação do plano: `proximasSituacoes`, `podeTransitar` e `transitar`.
