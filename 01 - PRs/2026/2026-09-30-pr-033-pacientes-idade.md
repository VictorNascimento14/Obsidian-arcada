---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 33
url: https://github.com/VictorNascimento14/Arcada/pull/33
branch: feat/pacientes-idade
tags: [pr, pacientes, idade]
status: aberto
---

# PR #33 — feat(pacientes): calcular a idade e a faixa etária pela data de nascimento

## 🎯 Contexto

Módulo 1 (Pacientes) do [[2026-09-30-plano-da-v1]], item 1.4: idade e faixa etária pela data de nascimento. Regra pura, sem tela, para o cartão da lista e o cabeçalho da ficha. Fecha a issue #27.

## 🔧 Mudanças

- `src/modulos/pacientes/idade.ts` — `idade`, `faixaEtaria`, `FAIXAS_ETARIAS` e o tipo `FaixaEtaria`.
- `src/modulos/pacientes/idade.test.ts` — 8 casos de aniversário, 6 de 29/02, o de nascimento futuro, 4 de formato inválido e os limites de cada faixa, também pela data de nascimento.

## 🕵️ Dado pessoal (LGPD)

Data de nascimento é dado pessoal. Os testes só usam datas escolhidas pelo limite que exercitam, sem ligação com pessoa, e o erro de formato não repete a data recebida.

## 🧠 Decisões técnicas

- Sem `Date`: a conta subtrai os anos e compara `MM-DD` como texto, que ordena como o calendário. Nenhum fuso entra, e `toISOString()` (UTC) fica de fora, como manda o CLAUDE.md do repositório.
- `hoje` é parâmetro, não relógio interno: a função fica pura e o teste fixa o dia. Quem chama passa `diaISO(new Date())` (`@/ui`).
- 29/02: quem nasceu nesse dia completa o ano em 1º/03 nos anos não bissextos — é o que sai de comparar mês e dia como um par, e a idade nunca anda para trás.
- `faixaEtaria` recebe os anos, não as datas: quem já calculou a idade para mostrá-la não repete a conta. `FAIXAS_ETARIAS` segue o formato de `GRUPOS_COLUNA` (chave sem acento, rótulo com acento) e o tipo `FaixaEtaria` sai das chaves.
- Data fora do formato `AAAA-MM-DD` lança `RangeError`, sem repetir o valor na mensagem. Sem a checagem, um nascimento vazio viraria `2026 anos` sem avisar. Só o formato é conferido: 30/02 passa, e a validade da data é de quem grava.
- Nascimento depois de `hoje` dá número negativo, sem cortar em zero: esconder a data futura mascararia o erro de quem gravou.

## ⚠️ Armadilhas e aprendizados

- `new Date("AAAA-MM-DD")` lê a data como UTC: no fuso do Brasil, `getDate()` devolve o dia anterior e o aniversário andaria um dia. Por isso a conta é por texto.
- Comparar só o dia, ou o dia antes do mês, erra quem faz aniversário num mês ainda por vir: nascido em 05/10, em 30/09 tem 35 anos, não 36. O teste cobre esse caso.

## 🧪 Como testar

1. `npm test -- src/modulos/pacientes` roda `idade.test.ts`: aniversário amanhã, hoje e ontem; mês do aniversário ainda por vir com dia maior; nascido em 29/02 em anos bissextos e não bissextos; os limites de 12, 18 e 60 anos pela data de nascimento; datas fora do formato.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[IdadeEFaixaEtaria]]
- [[2026]] (changelog)
