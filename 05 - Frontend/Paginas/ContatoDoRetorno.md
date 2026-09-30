---
tipo: funcionalidade
camada: frontend
area: Retornos
rota: /retornos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, retornos, whatsapp, contato]
---

# Contato do retorno

## O que é

Em cada linha da [[ListaDeRetornos]], dois links: **WhatsApp**, que abre a conversa com o paciente com a mensagem de
retorno pronta, e **Marcar consulta**, o atalho para a agenda. É o desenho da [[ConfirmacaoPeloWhatsApp]] da agenda: a
clínica dispara a mensagem do próprio WhatsApp, e nada sai por servidor do app.

## Onde está no código

- `src/modulos/retornos/mensagem.ts` — `mensagemDeRetorno(paciente)` e `linkDeRetorno(paciente)`, sobre o
  `linkWhatsApp` de `src/modulos/pacientes/contato.ts`.
- `ContatoDoRetorno.tsx` — os dois links. `ListaDeRetornos.tsx` os põe numa linha abaixo de cada paciente.

## Comportamento

- **A mensagem**: `Olá, Ana! Está na hora do seu retorno ao consultório. Vamos marcar um horário? É só responder esta
  mensagem.` Só o primeiro nome. Não cita procedimento, data nem prazo: nenhum dado de saúde sai do app. É a mesma para
  o retorno vencido e para o a vencer.
- **O WhatsApp só aparece quando o telefone serve**: `linkWhatsApp` devolve `null` para telefone vazio, curto demais ou
  fictício, como o DDD 00 das sementes. Sem ele, a linha fica só com **Marcar consulta**. O link abre em outra aba.
- **Marcar consulta** leva a `/agenda`. A agenda não lê parâmetro da URL, então ela não recebe o paciente: quem marca o
  escolhe lá.
- **Leitor de tela**: cada link diz de quem é — `WhatsApp de Ana Beatriz Moura, abre em outra aba` e `Marcar consulta para
  Ana Beatriz Moura` —, porque a lista repete os mesmos dois em toda linha. O nome vem num `sr-only` depois do texto
  visível.

## Movimento e micro-interações

Nenhum além do kit: os links têm a cara do botão `ghost` (`press` e o fundo no hover).

## Histórico de mudanças

- [[2026-09-30-pr-181-retornos-contato]] — a mensagem de retorno e os dois links em cada linha da lista.
