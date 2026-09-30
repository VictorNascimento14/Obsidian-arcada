---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, idade]
---

# Idade e faixa etária

## O que é

A conta da idade do paciente e a classificação em faixa etária, a partir da data de nascimento. Não
têm tela — a lista, a ficha e o cadastro do paciente as usam.

## Onde está no código

- `src/modulos/pacientes/idade.ts` — `idade`, `faixaEtaria`, `FAIXAS_ETARIAS` (o rótulo de cada
  faixa) e o tipo `FaixaEtaria`.
- `src/modulos/pacientes/idade.test.ts` — os testes.

## Comportamento

- **`idade(nascimento, hoje)`**: anos completos, com as duas datas em `AAAA-MM-DD`. No dia do
  aniversário o ano já conta; antes dele, um a menos. Quem nasceu em 29/02 completa o ano em 1º/03
  nos anos não bissextos.
- **`hoje`** sai de `diaISO(new Date())` (`@/ui`), nunca de `toISOString()`. A conta é por texto,
  sem `Date`, para o fuso não mexer no dia.
- **Data fora do formato** lança `RangeError` — sem repetir a data na mensagem, que é dado pessoal.
  Só o formato é conferido: 30/02 passa, e a validade da data é de quem grava.
- **Nascimento depois de `hoje`** dá número negativo; recusar a data futura é de quem grava.
- **`faixaEtaria(anos)`**: criança (até 11), adolescente (12 a 17), adulto (18 a 59) e idoso (60 ou
  mais). Recebe a idade em anos, não as datas: `faixaEtaria(idade(nascimento, hoje))`.
- **`FAIXAS_ETARIAS`** traz o rótulo de cada faixa (Criança, Adolescente, Adulto, Idoso); a chave é
  sem acento, como em `GRUPOS_COLUNA`.

## Histórico de mudanças

- [[2026-09-30-pr-033-pacientes-idade]] — `idade`, `faixaEtaria` e os testes.
- [[2026-09-30-pr-051-lista-de-pacientes]] — a lista mostra a idade no cartão, por `anosDoPaciente` e `rotuloIdade` (`exibicao.ts`).
- [[2026-09-30-pr-076-ficha-do-paciente]] — o cabeçalho da ficha mostra a idade.
