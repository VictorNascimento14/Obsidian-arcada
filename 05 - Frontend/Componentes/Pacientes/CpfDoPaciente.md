---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, cpf]
---

# CPF do paciente

## O que é

As regras puras de CPF do módulo Pacientes: limpar, mascarar e validar. Não têm tela — o cadastro, a
edição e a ficha do paciente as usam.

## Onde está no código

- `src/modulos/pacientes/cpf.ts` — `limparCpf`, `formatarCpf` e `cpfValido`.
- `src/modulos/pacientes/cpf.test.ts` — os testes. O CPF de cada caso é calculado no próprio teste;
  nenhum é escrito à mão.

## Comportamento

- **Guarda**: só os 11 dígitos, por `limparCpf(valor)`. A máscara `000.000.000-00` é de exibição
  (`formatarCpf`); comparar e buscar por CPF não depende de como foi digitado.
- **Máscara de digitação**: `formatarCpf` aceita entrada parcial, ignora o que não é dígito e corta em
  11 dígitos. O separador só aparece quando há dígito depois dele, para o backspace não travar.
- **Validação**: `cpfValido` aceita com ou sem máscara e exige 11 dígitos, os dois verificadores
  (módulo 11) e dígitos que não sejam todos iguais — as dez repetições fecham a conta e por isso a
  recusa é explícita.
- **Campo vazio**: `cpfValido("")` é `false`. O CPF é opcional no cadastro, então quem decide que o
  vazio vale é o formulário.

## Histórico de mudanças

- [[2026-09-30-pr-030-pacientes-cpf]] — `limparCpf`, `formatarCpf` e `cpfValido`, com os testes.
