---
tipo: funcionalidade
camada: frontend
area: Retornos
rota: /retornos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, retornos, adiar, dispensar]
---

# Adiar ou dispensar o retorno

## O que é

Na [[ListaDeRetornos]], cada paciente tem dois botões, **Adiar** e **Dispensar**, ao lado do [[ContatoDoRetorno]]. Adiar
empurra o retorno por N dias; dispensar tira o retorno da lista, com um motivo. Quem foi dispensado aparece num terceiro
cartão, **Dispensados**, com o botão **Reativar**, que desfaz as duas coisas. A regra de fundo está em
[[2026-09-30-adiar-e-dispensar-valem-so-para-o-retorno-em-curso]].

## Onde está no código

- `src/modulos/retornos/dados.ts` — a coleção `retornos` (um estado por paciente) e `adiarRetorno`, `dispensarRetorno` e
  `reativarRetorno`, que conferem tudo ao gravar.
- `estado.ts` — a regra pura: `estadoVigente`, `dataDoRetorno` e `adiadoPara`.
- `lista.ts` — `retornosPendentes` aplica o estado; `retornosDispensados` monta o cartão dos dispensados.
- `AdiarOuDispensar.tsx` — os dois botões e o campo que se abre na linha. `Dispensados.tsx` — o cartão.
  `ListaDeRetornos.tsx` monta tudo.

## Comportamento

- **Adiar** pede os dias (sete de saída; de 1 a 365). A conta parte da data que o retorno tem agora ou de hoje, a que
  for maior: o retorno que vence daqui a 20 dias e é adiado 7 passa a vencer daqui a 27, e o que já venceu passa a contar
  de hoje. Adiar de novo parte do dia já adiado. O retorno vale o dia novo — pode passar de vencido para a vencer — e a
  linha diz `retorno adiado para 15/10/2026`.
- **O adiado para além dos 30 dias** some da lista até entrar na janela. Não há cartão de adiados.
- **Dispensar** pede o motivo: obrigatório, sem as pontas, até 200 caracteres. O campo avisa para não pôr dado de saúde.
  O retorno sai dos dois cartões e entra em **Dispensados**, com o dia e o motivo, do mais recente ao mais antigo.
  Dispensar troca o adiamento que houvesse.
- **Reativar** apaga o estado, e o retorno volta ao dia da regra. Adiar um retorno dispensado é recusado
  (`Este retorno foi dispensado. Reative-o antes de adiar.`).
- **O estado vale só para o retorno em curso.** Ele guarda o dia do último atendimento de que o retorno partiu; se o
  paciente é atendido de novo, o retorno passa a contar do atendimento novo e o estado antigo deixa de valer. O cartão
  Dispensados também o omite.
- **Erros** aparecem no campo (`Informe de 1 a 365 dias.`, `Diga o motivo da dispensa.`) e somem ao digitar. Um aviso
  passageiro confirma o adiamento, a dispensa e a reativação.
- **Leitor de tela**: os botões repetem em toda linha, então cada um diz de quem é — `Adiar o retorno de Ana Beatriz
  Moura`, `Dispensar …`, `Reativar …` — pelo nome num `sr-only` depois do texto visível.

## Movimento e micro-interações

Nenhum além do kit. O campo abre na própria linha e recebe o foco; **Voltar** o fecha sem gravar. Sem modal: a linha
continua à vista.

## Histórico de mudanças

- [[2026-09-30-pr-187-retornos-adiar]] — adiar, dispensar e reativar, a coleção `retornos` e o cartão Dispensados.
