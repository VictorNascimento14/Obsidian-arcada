---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 153
url: https://github.com/VictorNascimento14/Arcada/pull/153
branch: feat/agenda-whatsapp
tags: [pr, agenda, whatsapp, confirmacao]
status: aberto
---

# PR #153 — feat(agenda): pedir a confirmação da consulta pelo WhatsApp

## 🎯 Contexto

Módulo 8 (Agenda) do [[2026-09-30-plano-da-v1]], item 8.7, no detalhe da consulta do [[2026-09-30-pr-118-agenda-situacao-tela]]. Fecha a issue #149.

## 🔧 Mudanças

- `confirmacao.ts` (novo): `mensagemDeConfirmacao` e `linkDeConfirmacao`, que chama o `linkWhatsApp` de `src/modulos/pacientes/contato.ts`.
- `DetalheDaConsulta.tsx`: o link, na linha de ações (que passa a se chamar Outras ações), junto de Remarcar e Cancelar consulta.
- Testes: 8 novos (4 em `confirmacao.test.ts` e 4 em `PaginaAgenda.test.tsx`).

## 🕵️ Dado pessoal (LGPD)

A mensagem sai do app para o WhatsApp do paciente e leva só o primeiro nome, o dia, o horário e o profissional. O procedimento fica de fora, porque é dado de saúde, e nenhum CPF ou dado clínico entra. O telefone sai do cadastro do paciente, que o app já guarda.

## 🧠 Decisões técnicas

- Reusa o `linkWhatsApp` que o módulo de pacientes exporta, em vez de uma função local: a normalização do telefone (máscara, `+55`, zero no DDD) fica em um só lugar, e é a mesma do botão WhatsApp da ficha do paciente.
- O link é um `<a>` com `target="_blank"` e `rel="noopener noreferrer"`, não um `<button>`, e avisa o leitor de tela que abre em outra aba, como a ficha do paciente.
- Aparece enquanto a consulta aguarda o atendimento (`aguardaAtendimento`, o mesmo critério do Remarcar), inclusive na confirmada, que pode ser lembrada. Depois que o atendimento começa, ou quando a consulta acaba, a mensagem não faz mais sentido.
- Enviar a mensagem não muda a situação: quem confirma é o paciente, respondendo, e a recepção então marca Confirmar consulta.
- O texto usa o `rotuloDoDia` da agenda (`quarta-feira, 30 de setembro de 2026`); o paciente é chamado pelo primeiro nome e o profissional só entra quando existe.
- Sem telefone válido o link some e nada explica por quê, como a ficha do paciente faz com o botão WhatsApp. O rótulo diz Enviar confirmação, e não Confirmar, para não se confundir com o botão Confirmar consulta, que muda a situação.

## ⚠️ Armadilhas e aprendizados

- As sementes usam telefone com DDD 00 de propósito, e `linkWhatsApp` devolve `null` para eles: a demonstração não mostra o link. Para ver, dê ao paciente um número de DDD válido, como o `(11) 91234-5678` dos testes de contato.
- O `linkWhatsApp` já codifica o texto com `encodeURIComponent`: a mensagem entra crua, com acento, e não se codifica antes.
- No celular o link tem 32 caracteres e não quebra (`whitespace-nowrap`): ele cabe na largura do modal em 390 px, conferido no navegador, e ocupa a própria linha.

## 🧪 Como testar

1. Numa consulta agendada de um paciente com telefone válido (edite um paciente para `(11) 91234-5678`), abra o detalhe em `/agenda`: aparece Enviar confirmação pelo WhatsApp, e o link abre `wa.me` com a mensagem pronta.
2. Nas consultas de demonstração (telefone com DDD 00), o link não aparece, e Remarcar e Cancelar consulta continuam lá.
3. Clique em Iniciar atendimento e reabra a consulta: o link some.
4. Rode `npx vitest run --maxWorkers=2 src/modulos/agenda`.

## 📎 Documentação afetada

- [[ConfirmacaoPeloWhatsApp]]
- [[DetalheDaConsulta]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
