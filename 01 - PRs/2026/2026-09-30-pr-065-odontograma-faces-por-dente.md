---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 65
url: https://github.com/VictorNascimento14/Arcada/pull/65
branch: feat/odontograma-faces
tags: [pr, odontograma, dominio]
status: merged
---

# PR #65 — feat(odontograma): definir as faces de cada dente e a validação delas

## 🎯 Contexto

Item 3.2 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): as faces por dente da decisão 3 do [[ADR-004-notacao-fdi-no-odontograma]], sobre a regra da notação do [[2026-09-30-pr-053-odontograma-notacao-fdi]]. O tipo `Face` já existia ([[2026-09-30-pr-025-tipos-do-dominio]]); faltava saber quais faces existem em cada dente. Fecha a issue #62.

## 🔧 Mudanças

- `src/dominio/fdi.ts` — `facesDoDente`, `faceValida` e `nomeFace`.
- `src/dominio/fdi.test.ts` — dente da frente e de trás, superior e inferior, permanente e decíduo; os 52 dentes contra uma conta independente (posição e quadrante); `RangeError` de `facesDoDente` para dente que não existe.
- [[FacesDoDente]] — nota nova de funcionalidade no cofre.

## 🧠 Decisões técnicas

- **As faces saem da arcada e do tipo do dente**, sem tabela de 52 linhas: `P` ou `L` por `arcada(n)`, `I` ou `O` por o dente ser incisivo ou canino. O decíduo entra sem caso especial, porque `tipoDente` já trata as posições 4 e 5 como molares ([[NotacaoFdi]]).
- **Ordem fixa: V, M, D, P ou L, I ou O** — a do glossário e do ADR-004. Quem desenha o dente pode ler as faces por posição.
- **`faceValida` recebe a face como texto e devolve `false` para dente que não existe**, sem lançar: serve à validação de dado guardado. `facesDoDente` continua lançando `RangeError`, como o resto da notação.
- **`nomeFace` recebe só a face, não o dente**: `L` é sempre lingual e `P`, palatina; quem escolheu entre as duas foi `facesDoDente`.

## ⚠️ Armadilhas e aprendizados

- A face de cima (`I` ou `O`) segue a **função** do dente — cortar ou mastigar —, não o ser molar: o pré-molar também tem `O`, e no decíduo as posições 4 e 5 são molares. Quem testar "é molar?" para escolher `O` erra os pré-molares.

## 🧪 Como testar

1. `npx vitest run src/dominio/fdi.test.ts` passa: as faces de um dente da frente e um de trás, superior e inferior, permanente e decíduo; nos 52 dentes, `I` só na frente, `O` só atrás, `P` só em cima e `L` só embaixo; face ou dente que não existe dá `false` em `faceValida`; os sete nomes.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.
3. Conferir à mão duas respostas: `facesDoDente(54)` é `V, M, D, P, O` (o 4 do decíduo já é molar) e `facesDoDente(43)` é `V, M, D, L, I`.

## 📎 Documentação afetada

- [[ADR-004-notacao-fdi-no-odontograma]]
- [[NotacaoFdi]]
- [[FacesDoDente]]
- [[2026]] (changelog)
