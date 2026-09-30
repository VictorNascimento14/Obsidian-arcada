---
tipo: funcionalidade
camada: frontend
area: Retornos
rota: /retornos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, retornos, lista]
---

# Lista de retornos

## O que é

A tela `/retornos`: quem deve voltar ao consultório. São dois cartões — **Vencidos** (o retorno já passou) e **A vencer
nos próximos 30 dias** —, cada um com os pacientes do prazo mais antigo ao mais próximo. O termo **Retorno** está no
[[glossario]]; a conta da data de cada paciente é a regra de [[2026-09-30-retorno-conta-do-ultimo-atendimento]].

## Onde está no código

- `src/modulos/retornos/modulo.ts` — a rota `/retornos` e o item **Retornos** da coluna (grupo Consultório, ordem 30,
  ícone `refresh`), sem aba na ficha.
- `PaginaRetornos.tsx` — a moldura da tela. `ListaDeRetornos.tsx` — os dois cartões.
- `lista.ts` — `retornosPendentes(pacientes, consultas, planos, hoje)`: quem entra, a situação e a ordem.
- `regra.ts` — `retornoDoPaciente`: a data do retorno de cada paciente.
- `sementes.ts` — quatro pacientes de demonstração, com atendimentos antigos.

## Comportamento

- **Quem entra**: o paciente já atendido (consulta concluída ou item de plano realizado) cujo retorno venceu ou vence
  em até 30 dias; o 30º dia conta. Quem nunca foi atendido não aparece, nem o retorno de quem saiu do cadastro.
- **Vencido** é o retorno com data antes de hoje. O que cai hoje é **a vencer**, com `Vence hoje`: como a parcela que
  vence hoje, ainda não é atraso.
- **Ordem**: pelo dia previsto — o vencido há mais tempo vem primeiro — e, no mesmo dia, pelo nome.
- **Cada linha**: avatar, o nome (link para a [[FichaDoPaciente]]), `Último atendimento em 16/02/2026 · retorno
  previsto em 16/08/2026` e a pílula do prazo: `Vencido há 45 dias`, `Vence hoje` ou `Vence em 9 dias`. A lista não
  mostra o procedimento: é dado de saúde, e quem chama o paciente não precisa dele.
- **Estados vazios**: `Nenhum retorno vencido.` e `Nenhum retorno a vencer nos próximos 30 dias.`
- **O dia de hoje** é o da última renderização (`diaISO`).
- **Quem já tem consulta marcada** continua na lista até ser atendido, porque a regra olha só o que já foi feito.

## Movimento e micro-interações

Nenhum além do kit: a tela só usa os cartões de vidro, sem animação própria.

## Histórico de mudanças

- [[2026-09-30-pr-176-retornos-lista]] — a tela, a regra da lista e as sementes de demonstração.
