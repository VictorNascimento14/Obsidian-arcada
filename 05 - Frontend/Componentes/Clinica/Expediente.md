---
tipo: funcionalidade
camada: frontend
area: Clinica
rota: /clinica
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, clinica, expediente]
---

# Expediente

## O que é

O cartão dos dias e horários de atendimento na tela [[Clinica]]: para cada dia da semana, de segunda a
domingo, a clínica está fechada ou abre e fecha num horário, com um intervalo (o almoço) opcional. É o
`Expediente` que a agenda lê para sugerir horários livres.

## Onde está no código

- `src/modulos/clinica/Expediente.tsx` — o cartão (`ExpedienteDaClinica`): sete linhas, cada uma com a caixa
  «Aberto» e os quatro campos de hora.
- `src/modulos/clinica/expediente.ts` — `camposDoExpediente`, `validarDia`, `validarExpediente`,
  `salvarExpediente` e a lista `DIAS`.
- `src/dominio/clinica.ts` — o tipo `Expediente` (`Record<DiaDaSemana, FaixaHoraria[]>`) e `FaixaHoraria`.
- Dados: o campo `expediente` do registro único da coleção `clinica` (`CLINICA_ID`), em `src/dados/colecoes.ts`.

## Comportamento

- **Um dia**: a caixa «Aberto» liga e desliga o dia. Aberto, mostra Abertura, Fechamento, Início do intervalo e
  Fim do intervalo, todos campos de hora do navegador (`HH:mm`). Fechado, não mostra horário nenhum.
- **Como fica gravado**: dia fechado é lista vazia; aberto é uma faixa (abertura até fechamento); aberto com
  intervalo são duas (abertura até o início do intervalo, e fim do intervalo até o fechamento). Domingo é o dia
  `0`, como em `Date.getDay()`, embora a tela liste de segunda a domingo.
- **Regras** (`validarDia`, em `expediente.ts`): horário em `HH:mm`, de `00:00` a `23:59`; o fechamento vem
  **depois** da abertura (igual não vale); o intervalo tem os dois horários ou nenhum, termina depois de começar
  e cai **estritamente dentro** do dia — começar junto com a abertura, ou terminar junto com o fechamento,
  deixaria uma faixa vazia. Dia fechado não é validado.
- **Salvar expediente** grava a semana inteira de uma vez. Com qualquer erro, cada dia mostra a sua mensagem,
  aparece «Revise os dias destacados.» e nada é gravado, nem os dias que estavam certos. Sem erro, grava e avisa
  «Expediente salvo». O resto do registro da clínica não é tocado.
- **Dia fechado** guarda, só no formulário, o horário sugerido 08:00 às 18:00: ao marcar «Aberto», a pessoa ajusta
  em vez de digitar do zero.
- **Um intervalo por dia.** Um expediente com três faixas ou mais (que este formulário não produz) abre com a
  primeira abertura, o último fechamento e o vão entre as duas primeiras faixas; salvar regrava com uma pausa só.
- **Abre com o que está salvo.** O expediente é lido ao montar a tela; mudança vinda de outra aba não sobrescreve
  o que está sendo digitado.

## Movimento e micro-interações

O cartão entra subindo quando aparece (`GlassCard`); ao salvar, um aviso passageiro confirma.

## Histórico de mudanças

- [[2026-09-30-pr-107-expediente-da-clinica]] — o cartão de expediente, depois dos dados da clínica: horário por dia da semana, com intervalo opcional.
