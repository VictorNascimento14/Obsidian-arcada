---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 31
url: https://github.com/VictorNascimento14/Arcada/pull/31
branch: feat/agenda-feriados
tags: [pr, agenda, feriados]
status: merged
---

# PR #31 — feat(agenda): calcular os feriados nacionais a partir da Páscoa

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.10 — só a parte pura; o bloqueio na tela vem com "Marcar consulta" (8.5). Fecha a issue #29.

## 🔧 Mudanças

- `src/modulos/agenda/feriados.ts` — `pascoa` (Meeus/Jones/Butcher), `feriadosDoAno` e `feriadoDoDia`; o tipo `Feriado` (`dia`, `nome` e `tipo`: `feriado` ou `facultativo`).
- `src/modulos/agenda/feriados.test.ts` — 30 testes.

## 🧠 Decisões técnicas

- `tipo` separa o que bloqueia (`feriado`) do que só avisa (`facultativo`: Carnaval e Corpus Christi): quem marca a consulta decide com uma comparação, sem lista de exceções na tela.
- Datas por aritmética em UTC (`Date.UTC` e `getUTC*`), sem `toISOString()` nem `Date` local: o resultado não muda com o fuso nem com a hora do dia. Conferido rodando os testes em três fusos.
- Ano fora de 1583–9999 (Páscoa gregoriana; `AAAA` de quatro dígitos): `feriadosDoAno` devolve `[]` e `feriadoDoDia` devolve `undefined`, então a tela não quebra com data estranha. `pascoa`, que não tem resposta vazia, lança `RangeError`.
- `feriadoDoDia` compara a string inteira com a data calculada: `2026-9-7`, `07/09/2026` e texto vazio não casam com nada, sem regex.
- Só os feriados nacionais, como o item pede; estadual e municipal dependem da cidade da clínica e ficam de fora.

## ⚠️ Armadilhas e aprendizados

- O Dia da Consciência Negra só é feriado nacional desde a Lei 14.759/2023: o 20/11 entra a partir de 2024. Sem isso, navegar por anos anteriores marcaria um feriado que não existia.
- A Sexta-feira Santa pode cair no Tiradentes (21/04/2000; de novo em 2079). A lista do ano traz os dois e `feriadoDoDia` devolve o primeiro (Tiradentes); os dois são `feriado`, então o dia segue bloqueado.
- O Carnaval é a terça-feira (Páscoa − 47); a segunda-feira não entra.
- A fórmula da Páscoa sem o termo de correção `m` erra uma semana em 1954, 1981, 2049 e 2076; os quatro anos estão no teste.

## 🧪 Como testar

1. `npm test -- src/modulos/agenda` → 30 testes de `feriados.test.ts`: Páscoa de 12 anos conhecidos, os 12 feriados de 2026 em ordem, ponto facultativo, fevereiro bissexto, Consciência Negra só desde 2024, Sexta-feira Santa no Tiradentes (2000) e datas mal formadas.
2. `TZ=Pacific/Kiritimati npm test -- src/modulos/agenda` (e `America/Sao_Paulo`, `Pacific/Pago_Pago`) → o mesmo resultado: nada depende do fuso.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
