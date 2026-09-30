---
tipo: funcionalidade
camada: frontend
area: Comuns
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, comuns, dominio]
---

# TiposDoDominio

## O que é

O vocabulário de registros que mais de um módulo usa: paciente, clínica, profissional, cadeira, procedimento,
consulta, plano de tratamento e lançamento, mais o número de dente FDI e a face. São só tipos TypeScript, sem
React e sem regra — cada módulo põe a sua regra por cima. O significado dos termos está no [[glossario]].

## Onde está no código

Um arquivo por entidade, para que PRs de módulos diferentes não disputem o mesmo arquivo. Quem precisa de um
campo novo edita o arquivo da entidade, no PR do módulo dono dela.

| Arquivo (`src/dominio/`) | O que define |
|---|---|
| `odontologia.ts` | `NumeroDente` (número FDI, [[ADR-004-notacao-fdi-no-odontograma]]) e `Face` |
| `paciente.ts` | `Paciente` |
| `clinica.ts` | `Clinica`, `Profissional`, `Cadeira`, `Expediente`, `FaixaHoraria` e `DiaDaSemana` |
| `procedimento.ts` | `Procedimento` |
| `consulta.ts` | `Consulta` e `SituacaoConsulta` |
| `tratamento.ts` | `PlanoTratamento`, `ItemPlano` e `SituacaoPlano` |
| `financeiro.ts` | `Lancamento` e `FormaPagamento` |
| `datas.ts` | `DataISO`, `HoraISO` e `DataHoraISO` |
| `dinheiro.ts` | `Centavos` e as funções de [[Dinheiro]] |
| `index.ts` | barril: `import type { Consulta } from "@/dominio"` |

## Comportamento

- **Todo registro guardado tem `id: string`, e a ligação entre registros é por id.** A consulta aponta para o
  paciente, o profissional e a cadeira (`pacienteId`, `profissionalId`, `cadeiraId`); o plano, para o paciente;
  o item do plano, para o procedimento (`procedimentoId`); o lançamento, para o paciente e o plano (`planoId`).
  `Expediente` e `FaixaHoraria` não são registros: moram dentro da `Clinica`.
- **Data é `string` no horário local:** `DataISO` é `AAAA-MM-DD`, `HoraISO` é `HH:mm` e `DataHoraISO` é
  `AAAA-MM-DDTHH:mm`. A string ordena como o calendário. O dia de hoje sai de `diaISO` (`@/ui`), nunca de
  `toISOString()`, que à noite no Brasil já devolve o dia seguinte.
- **Dinheiro é `Centavos`, inteiro** ([[Dinheiro]]). Preço e desconto guardam centavos; o total do plano não se
  guarda — é a soma dos preços dos itens menos o desconto. O item do plano guarda a própria cópia do preço, para
  que reajustar o catálogo não mude o que já foi orçado.
- **Opcional só onde o dado pode faltar:** CPF, e-mail e convênio no paciente (sem convênio é particular); dente e
  face no item do plano; `pagoEm` e `forma` no lançamento em aberto, que nascem juntos na baixa.
- **Listas fechadas.** Os valores são estes; quem decide as transições é o módulo dono.

  | Tipo | Valores | Transições |
  |---|---|---|
  | `SituacaoConsulta` | `agendada`, `confirmada`, `em-atendimento`, `concluida`, `faltou`, `cancelada` | item 8.6 |
  | `SituacaoPlano` | `proposto`, `aprovado`, `em-andamento`, `concluido`, `recusado` | item 7.6 |
  | `FormaPagamento` | `dinheiro`, `pix`, `debito`, `credito` | — |

- **Expediente por dia da semana.** `Expediente` é `Record<DiaDaSemana, FaixaHoraria[]>`, com `DiaDaSemana` como em
  `Date.getDay()` (0 é domingo). Os sete dias estão sempre presentes: lista vazia é dia fechado, e duas faixas
  dão o intervalo de almoço.
- **Sem regra aqui.** Que números FDI existem (item 3.1), a validação do CPF (1.2) e do CRO (5.6) e o
  parcelamento (7.5) vêm nos PRs dos módulos. O tipo também não protege: um `number` pode ser fracionário e uma
  data pode ser `2026-9-3`. A validação é da escrita, em `src/dados/`.

## Histórico de mudanças

- [[2026-09-30-pr-025-tipos-do-dominio]] — tipos do domínio criados, com o dinheiro em centavos.
