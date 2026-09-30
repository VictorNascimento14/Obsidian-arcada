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
consulta, plano de tratamento e lançamento, mais o número de dente FDI e a face. Os registros são só tipos
TypeScript, sem React e sem regra — cada módulo põe a sua regra por cima; ao lado deles ficam só as regras que
mais de um módulo usa (o dinheiro, a notação FDI e a de ativo do profissional e da cadeira). O significado dos
termos está no [[glossario]].

## Onde está no código

Um arquivo por entidade, para que PRs de módulos diferentes não disputem o mesmo arquivo. Quem precisa de um
campo novo edita o arquivo da entidade, no PR do módulo dono dela.

| Arquivo (`src/dominio/`) | O que define |
|---|---|
| `odontologia.ts` | `NumeroDente` (número FDI, [[ADR-004-notacao-fdi-no-odontograma]]) e `Face` |
| `fdi.ts` | a regra da notação FDI e as faces de cada dente ([[NotacaoFdi]], [[FacesDoDente]]) |
| `paciente.ts` | `Paciente` |
| `clinica.ts` | `Clinica`, `Profissional`, `Cadeira`, `Expediente`, `FaixaHoraria` e `DiaDaSemana`, mais `profissionalAtivo` e `cadeiraAtiva` |
| `procedimento.ts` | `Procedimento` |
| `consulta.ts` | `Consulta` e `SituacaoConsulta` |
| `tratamento.ts` | `PlanoTratamento`, `ItemPlano` (com `faces` e `realizadoEm`) e `SituacaoPlano` |
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
  guarda — é a soma dos preços dos itens menos o desconto ([[TotaisDoPlano]]). O item do plano guarda a própria
  cópia do preço, para que reajustar o catálogo não mude o que já foi orçado.
- **Opcional só onde o dado pode faltar:** CPF, e-mail, convênio e observações no paciente (sem convênio é
  particular); telefone, endereço, cidade e UF na clínica; especialidade e `ativo` no profissional e `ativa` na
  cadeira; código e `condicaoResultante` no procedimento; dente e `faces` no item do plano — uma lista, porque um
  item pode levar mais de uma face — e `realizadoEm`, o dia em que o item foi feito (ausente é ainda a fazer);
  `pagoEm` e `forma` no lançamento em aberto, que nascem juntos na baixa.
- **Ativo é o padrão.** Profissional sem `ativo` e cadeira sem `ativa` — a semente, o dado gravado antes de o
  campo existir — contam como ativos: leia por `profissionalAtivo` e `cadeiraAtiva`, e não pelo campo, que
  esconderia quem veio sem ele.
- **Listas fechadas.** Os valores são estes; quem decide as transições é o módulo dono.

  | Tipo | Valores | Transições |
  |---|---|---|
  | `SituacaoConsulta` | `agendada`, `confirmada`, `em-atendimento`, `concluida`, `faltou`, `cancelada` | `src/modulos/agenda/situacao.ts` ([[2026-09-30-pr-061-situacao-da-consulta]]) |
  | `SituacaoPlano` | `proposto`, `aprovado`, `em-andamento`, `concluido`, `recusado` | [[SituacaoDoPlano]] |
  | `FormaPagamento` | `dinheiro`, `pix`, `debito`, `credito` | — |

- **Expediente por dia da semana.** `Expediente` é `Record<DiaDaSemana, FaixaHoraria[]>`, com `DiaDaSemana` como em
  `Date.getDay()` (0 é domingo). Os sete dias estão sempre presentes: lista vazia é dia fechado, e duas faixas
  dão o intervalo de almoço.
- **Pouca regra aqui.** Só a que mais de um módulo usa: o dinheiro ([[Dinheiro]]), a notação FDI e as faces
  ([[NotacaoFdi]], [[FacesDoDente]]) e a de ativo do profissional e da cadeira. O resto mora no módulo dono: a
  validação do CPF em `src/modulos/pacientes/cpf.ts` ([[CpfDoPaciente]]), a do CRO em
  `src/modulos/clinica/cro.ts` e o parcelamento em `src/modulos/tratamentos/parcelas.ts`
  ([[ParcelamentoDoOrcamento]]). O tipo também não protege: um `number` pode ser fracionário e uma data pode ser
  `2026-9-3`. A validação é da escrita, em `src/dados/`.

## Histórico de mudanças

- [[2026-09-30-pr-025-tipos-do-dominio]] — tipos do domínio criados, com o dinheiro em centavos.
- [[2026-09-30-pr-048-dados-da-clinica]] — `Clinica` ganha telefone, endereço, cidade e UF.
- [[2026-09-30-pr-049-tratamentos-plano]] — `ItemPlano` ganha `realizadoEm`.
- [[2026-09-30-pr-053-odontograma-notacao-fdi]] — `fdi.ts`: a regra da notação FDI.
- [[2026-09-30-pr-060-tratamentos-situacao]] — as transições de `SituacaoPlano` ganham regra, em [[SituacaoDoPlano]].
- [[2026-09-30-pr-061-situacao-da-consulta]] — as transições de `SituacaoConsulta` ganham regra, em `src/modulos/agenda/situacao.ts`.
- [[2026-09-30-pr-064-profissionais-da-clinica]] — `Profissional` ganha `especialidade` e `ativo`, e a regra `profissionalAtivo`.
- [[2026-09-30-pr-065-odontograma-faces-por-dente]] — `fdi.ts` ganha as faces de cada dente.
- [[2026-09-30-pr-069-cadeiras-da-clinica]] — `Cadeira` ganha `ativa`, e a regra `cadeiraAtiva`.
- [[2026-09-30-pr-071-cadastro-de-paciente]] — `Paciente` ganha `observacoes`.
- [[2026-09-30-pr-074-catalogo-padrao-de-procedimentos]] — `Procedimento` ganha `codigo` e `condicaoResultante`.
