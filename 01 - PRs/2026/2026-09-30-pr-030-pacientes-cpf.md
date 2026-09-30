---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 30
url: https://github.com/VictorNascimento14/Arcada/pull/30
branch: feat/pacientes-cpf
tags: [pr, pacientes, cpf, lgpd]
status: aberto
---

# PR #30 — feat(pacientes): validar e mascarar CPF com dígitos verificadores

## 🎯 Contexto

Módulo 1 (Pacientes) do [[2026-09-30-plano-da-v1]], item 1.2: validação e máscara de CPF. Regra pura, sem tela, para o cadastro e a ficha do paciente. Fecha a issue #26.

## 🔧 Mudanças

- `src/modulos/pacientes/cpf.ts` — `limparCpf`, `formatarCpf` e `cpfValido`.
- `src/modulos/pacientes/cpf.test.ts` — 26 testes; todo CPF usado nasce de uma função do teste que completa 9 dígitos com os dois verificadores.

## 🕵️ Dado pessoal (LGPD)

CPF é dado pessoal. O teste **calcula** cada CPF que usa a partir de 9 dígitos: nenhum é escrito à mão, e nenhum aparece neste PR, na nota ou no changelog. CPF também não entra em semente (regra do repositório).

## 🧠 Decisões técnicas

- Guarda só os 11 dígitos (`limparCpf`); a máscara é de exibição. Comparar e buscar por CPF não depende de como foi digitado, e quem grava deve guardar `limparCpf(valor)`.
- `cpfValido` aceita com ou sem máscara: descarta tudo que não é dígito antes de conferir. Vazio devolve `false`; o CPF é opcional no cadastro, então quem trata o campo vazio é o formulário.
- `formatarCpf` só põe o separador quando há dígito depois dele e corta em 11 dígitos: serve para aplicar a cada tecla, e o backspace não trava num ponto que a máscara devolveria.
- Uma função só calcula os dois verificadores, com pesos de `base.length + 1` até 2: a mesma conta serve ao primeiro (9 dígitos) e ao segundo (10 dígitos).
- A conta do teste está escrita de outro jeito que a do módulo (`(soma × 10) mod 11`, pesos contados da direita), para o teste não repetir o código que confere.

## ⚠️ Armadilhas e aprendizados

- Os 11 dígitos iguais passam na conta do módulo 11: nas dez repetições (`000…` a `999…`) os dois verificadores são o próprio dígito. Sem a recusa explícita, `000.000.000-00` seria válido; o teste prova que a conta fecha para as dez e que `cpfValido` recusa mesmo assim.
- A conta do módulo 11 não detecta toda troca de um dígito: no segundo cálculo o primeiro dígito pesa 11 (que vale 0 no módulo 11), e o resto 0 e o 1 dão o mesmo verificador 0. Por força bruta, trocar o primeiro dígito por mais ou menos 1 às vezes gera outro CPF válido. Por isso o teste de "verificador trocado" mexe só nos dois últimos dígitos, onde a recusa é garantida.

## 🧪 Como testar

1. `npm test -- src/modulos/pacientes` roda os 26 testes de `cpf.test.ts`: máscara parcial, mil CPFs calculados aceitos, verificador trocado recusado e as dez repetições (`000…` a `999…`) recusadas.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.
3. Para conferir que o teste tem dentes: apague a recusa dos 11 dígitos iguais em `cpfValido` e rode de novo — os dez casos de repetição falham.

## 📎 Documentação afetada

- [[CpfDoPaciente]]
- [[2026]] (changelog)
