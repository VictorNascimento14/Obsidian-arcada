---
tipo: aprendizado
data: 2026-09-30
contexto: PR #187 — adiar ou dispensar retorno
tags: [aprendizado, retornos, estado]
---

# Adiar e dispensar valem só para o retorno em curso

Origem: [[2026-09-30-pr-187-retornos-adiar]]. Regra de fundo: [[2026-09-30-retorno-conta-do-ultimo-atendimento]].

## O sintoma que o desenho evita

Se "dispensado" fosse uma marca no paciente, quem a clínica dispensou em março e voltou a ser atendido em agosto nunca
mais entraria na lista de retornos: a marca de uma decisão antiga esconderia um retorno novo. Com "adiado" é o mesmo,
o adiamento de uma data que já não existe.

## A regra

- O estado (`arcada:retornos`, um por paciente) guarda o **dia do último atendimento** de que o retorno partiu. Só vale
  enquanto o último atendimento do paciente for esse (`estadoVigente`, em `src/modulos/retornos/estado.ts`). Outro
  atendimento abre um retorno novo, e o estado antigo fica no armazenamento sem efeito: ninguém precisa apagá-lo.
- **Adiar conta da data que o retorno tem agora ou de hoje, a que for maior.** Somar N ao dia já vencido deixaria o
  retorno vencido, e "adiar" não adiaria nada; somar a hoje um retorno que vence em 20 dias o adiantaria. O retorno vale o
  maior entre o dia da regra e o dia adiado.
- **Escrever confere contra os dados vivos**: `adiarRetorno` e `dispensarRetorno` recalculam o retorno na hora de
  gravar e recusam o paciente que não tem um, em vez de confiar no que a tela mostrava.

## Como evitar o erro

- Estado de um processo que se repete (retorno, revisão, lembrete) **carrega a rodada a que pertence**. Uma marca sem a
  rodada vira permanente.
- Antes de "somar N dias a uma data", pergunte de que data quem pede acha que está somando.
