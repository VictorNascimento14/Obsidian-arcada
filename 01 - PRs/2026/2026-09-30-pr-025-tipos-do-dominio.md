---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 25
url: https://github.com/VictorNascimento14/Arcada/pull/25
branch: feat/tipos-do-dominio
tags: [pr, fundacao, dominio]
status: aberto
---

# PR #25 — feat(dominio): definir os tipos do domínio e o dinheiro em centavos

## 🎯 Contexto

Item 0.9 do [[2026-09-30-plano-da-v1]] (módulo 0, fundação): os tipos e o dinheiro que os módulos 1 a 14 compartilham. Regra de módulo (lista de dentes FDI, CPF, parcelamento) não entra: vem nos PRs deles. Fecha a issue #9.

## 🔧 Mudanças

- `src/dominio/odontologia.ts` — `NumeroDente` (número FDI, só `number`) e `Face` (V, L, P, M, D, O, I).
- `src/dominio/paciente.ts` — `Paciente`; CPF, e-mail e convênio são opcionais.
- `src/dominio/clinica.ts` — `Clinica`, `Profissional` (com `cro` e `cor`), `Cadeira` e `Expediente` (por dia da semana, com `FaixaHoraria` e `DiaDaSemana`).
- `src/dominio/procedimento.ts` — `Procedimento`: especialidade, preço em centavos, duração em minutos, se exige dente e face, ativo.
- `src/dominio/consulta.ts` — `Consulta` e `SituacaoConsulta`.
- `src/dominio/tratamento.ts` — `PlanoTratamento`, `ItemPlano` (dente e face opcionais, com o preço do item) e `SituacaoPlano`.
- `src/dominio/financeiro.ts` — `Lancamento` (vencimento; `pagoEm` e `forma` opcionais) e `FormaPagamento`.
- `src/dominio/datas.ts` — os aliases `DataISO`, `HoraISO` e `DataHoraISO`.
- `src/dominio/dinheiro.ts` e `dinheiro.test.ts` — `Centavos`, `formatarReais`, `paraCentavos` e `somarCentavos`, com teste.
- `src/dominio/index.ts` — barril: tudo se importa de `@/dominio`.

## 🕵️ Dado pessoal (LGPD)

O PR só define o formato do dado: nada é gravado nem exibido. `Paciente` guarda nome, nascimento, telefone e, se informados, CPF e e-mail — dado pessoal; quem grava é `src/dados/`, e a semente é fictícia (`Paciente Exemplo`), sem CPF.

## 🧠 Decisões técnicas

- **Um arquivo por entidade.** PRs de módulos diferentes (por exemplo, o dos profissionais e o da agenda) editam arquivos diferentes; o `index.ts` só reexporta com `export *`.
- **Data é `string` com alias documentado, não `Date`** (`DataISO` `AAAA-MM-DD`, `HoraISO` `HH:mm`, `DataHoraISO` `AAAA-MM-DDTHH:mm`, todos no horário local): o dado vai e volta do `localStorage` como JSON, e a string ordena como o calendário. Os aliases moram em `datas.ts`, e não numa entidade, porque servem a paciente, clínica, consulta e financeiro.
- **`Centavos` é alias de `number`, não tipo marcado.** O [[ADR-005-dinheiro-em-centavos-inteiros]] aceita que o tipo não proteja; a defesa é a conversão nas bordas (`paraCentavos`, `formatarReais`) e a validação na escrita, em `src/dados/`.
- **`paraCentavos` recusa em vez de adivinhar.** Ponto decimal (`12.50`), três casas, sinal, milhar que começa em zero (`0.500`) e valor além do inteiro exato viram `null`. No Brasil o ponto é milhar: `12.500` são R$ 12.500,00, e `12.50` não é valor.
- **`ItemPlano.preco` é cópia do preço do catálogo** no dia em que o item entrou no plano: reajustar o catálogo (item 6.4) não muda o que já foi orçado. O total do plano não se guarda; é a soma dos itens menos o `desconto`.
- **`Lancamento` é a parcela do orçamento aprovado** ([[glossario]]). `pagoEm` e `forma` são opcionais e nascem juntos, na baixa; o estorno da baixa tira os dois.
- **`Expediente` é `Record<DiaDaSemana, FaixaHoraria[]>`**: os sete dias sempre presentes (dia fechado é lista vazia) e a agenda consulta por `Date.getDay()` sem procurar numa lista.
- **Sem regra de módulo.** Os tipos só nomeiam: a lista de dentes FDI (3.1), a validação de CPF (1.2) e do CRO (5.6), o parcelamento (7.5) e as transições de situação (7.6 e 8.6) vêm nos PRs dos módulos.

## ⚠️ Armadilhas e aprendizados

- O Intl separa o `R$` do número com espaço não separável (U+00A0): o teste que compara com o espaço comum reprova. Detalhe e correção em [[2026-09-30-intl-separa-o-real-com-espaco-nao-separavel]].
- O ponto é sempre milhar: `1.234` são R$ 1.234,00, e quem digita `1.5` recebe `null` (não R$ 1,50 nem R$ 150,00). Ver [[Dinheiro]].
- O tipo de dinheiro e o de data não protegem o valor — `12.5` centavos e `2026-9-3` passam no compilador —, então a validação é da escrita, em `src/dados/` ([[TiposDoDominio]]).

## 🧪 Como testar

1. `npm test` → `dinheiro.test.ts` passa: a formatação com o espaço não separável depois do `R$`, os formatos de entrada aceitos e recusados, o limite do inteiro exato e a soma que não erra centavo.
2. `npm run type-check` → os tipos compilam; qualquer um se importa por `@/dominio`, por exemplo `import type { Consulta } from "@/dominio"`.
3. `npm run lint && npm run build` passam.

## 📎 Documentação afetada

- [[ADR-004-notacao-fdi-no-odontograma]]
- [[ADR-005-dinheiro-em-centavos-inteiros]]
- [[glossario]]
- [[TiposDoDominio]]
- [[Dinheiro]]
- [[2026-09-30-intl-separa-o-real-com-espaco-nao-separavel]]
- [[2026]] (changelog)
