---
tipo: funcionalidade
camada: frontend
area: Agenda
rota: /agenda (modal)
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, agenda, whatsapp, confirmacao]
---

# Confirmação pelo WhatsApp

## O que é

O link **Enviar confirmação pelo WhatsApp** do [[DetalheDaConsulta]]: abre o WhatsApp do paciente com a mensagem
pronta pedindo que ele confirme a consulta. A recepção não digita o texto nem o telefone.

## Onde está no código

- `src/modulos/agenda/confirmacao.ts` — `mensagemDeConfirmacao` (o texto) e `linkDeConfirmacao` (o link).
- `src/modulos/agenda/DetalheDaConsulta.tsx` — o link, na linha de ações, junto de **Remarcar** e **Cancelar consulta**.
- `src/modulos/pacientes/contato.ts` — `linkWhatsApp`, que o link reaproveita: ele limpa a máscara, tira o `+55` e o
  zero do DDD e devolve `null` para o telefone que não serve. É o mesmo botão **WhatsApp** da ficha do paciente.

## Comportamento

- **A mensagem**: `Olá, Ana! Confirmamos sua consulta em quarta-feira, 30 de setembro de 2026 às 08:00 com Dra.
  Exemplo. Responda esta mensagem para confirmar sua presença ou, se precisar remarcar, é só avisar.` O paciente é
  chamado pelo primeiro nome, o dia sai por extenso como no título da agenda, e o profissional só entra quando existe.
- **O link** é `https://wa.me/55…?text=…`, com o texto codificado, e abre em outra aba (`target="_blank"`); o leitor de
  tela ouve `, abre em outra aba`.
- **Quando aparece**: enquanto a consulta aguarda o atendimento (agendada ou confirmada) **e** o telefone do paciente
  serve para o WhatsApp. Telefone vazio, curto demais ou com DDD 00 (o das sementes, de propósito) esconde o link, e o
  resto da linha continua. Depois que o atendimento começa, ou quando a consulta acaba, o link some.
- **Não muda a situação**: enviar a mensagem não confirma nada. Quem confirma é o paciente, respondendo; a recepção
  então usa **Confirmar consulta**.
- **Dado pessoal**: a mensagem sai do app e leva só o primeiro nome, o dia, o horário e o profissional. O procedimento
  fica de fora (é dado de saúde), e nenhum CPF ou dado clínico entra.
- **Não faz**: acompanhar se a mensagem foi enviada ou respondida, nem escolher outro modelo de texto.

## Movimento e micro-interações

O link tem a cara do botão `ghost` do kit (o mesmo de **Remarcar** e **Cancelar consulta**), com o ícone do WhatsApp; usa o
`press` e escurece de leve no `hover`. No celular ele ocupa a própria linha.

## Histórico de mudanças

- [[2026-09-30-pr-153-agenda-whatsapp]] — a confirmação pelo WhatsApp.
