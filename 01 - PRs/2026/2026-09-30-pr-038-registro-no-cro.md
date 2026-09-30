---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 38
url: https://github.com/VictorNascimento14/Arcada/pull/38
branch: feat/clinica-cro
tags: [pr, clinica, cro]
status: merged
---

# PR #38 — feat(clinica): validar o formato do registro no CRO

## 🎯 Contexto

Módulo 5 (Clínica e equipe) do [[2026-09-30-plano-da-v1]], item 5.6. Fecha a issue #37.

## 🔧 Mudanças

- `src/modulos/clinica/cro.ts` — `UFS` (as 27 siglas), `croValido` e `formatarCro`.
- `src/modulos/clinica/cro.test.ts` — as 27 UFs, as formas aceitas e recusadas e a normalização.

## 🧠 Decisões técnicas

- `croValido` confere só a forma canônica (`CRO-SP 12345`, maiúsculas, sem espaço nas pontas): é a que se guarda. Quem lê texto digitado passa antes por `formatarCro`.
- `formatarCro` aceita o que se digita na prática — minúsculas, `/`, hífen ou espaço entre `CRO` e a UF, UF e número colados, `CRO` omitido — e só devolve a forma canônica se `croValido` a aceita. Senão devolve o texto aparado, sem inventar um registro: `no 123` não vira `CRO-NO 123`.
- Zeros à esquerda ficam (`CRO-SP 00123`): o número é dado do conselho, não um inteiro para normalizar.
- `UFS` é exportada para o cadastro da clínica (cidade/UF) reaproveitar; se outro módulo precisar, ela sobe para `src/dominio/`.
- O texto digitado é aparado antes da expressão regular, que fica sem `\s*` nas pontas: nenhuma entrada, por longa que seja, faz a regex voltar atrás em tempo quadrático.

## ⚠️ Armadilhas e aprendizados

- O exemplo fictício do CLAUDE.md (`CRO-UF 00000`) não é um registro válido: `UF` não é sigla. Semente, teste e formulário de exemplo precisam de uma UF de verdade, como `CRO-SP 00000`.
- Formato válido não é registro confirmado: o app não consulta o conselho, então nada na tela deve sugerir que o CRO foi verificado.

## 🧪 Como testar

1. `npm test -- src/modulos/clinica` → `cro.test.ts`: as 27 UFs, as formas aceitas e recusadas e a normalização do que se digita.
2. Conferir à mão: `formatarCro("cro sp 12345")` → `CRO-SP 12345` e `croValido("CRO-XX 12345")` → `false`.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
