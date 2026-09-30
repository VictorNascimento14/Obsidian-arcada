---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 136
url: https://github.com/VictorNascimento14/Arcada/pull/136
branch: feat/atendimento-iniciar
tags: [pr, atendimento, agenda, ficha]
status: merged
---

# PR #136 — feat(atendimento): iniciar o atendimento pela consulta

## 🎯 Contexto

Item 9.1 (Iniciar pela consulta) do [[2026-09-30-plano-da-v1]]: da consulta agendada ou confirmada abre a tela de atendimento e move a consulta para `em-atendimento`. Fecha a issue #130.

## 🔧 Mudanças

- `src/modulos/atendimento/modulo.ts`: a rota `/atendimento/:consultaId` e a aba `Atendimentos` da ficha (`abaPaciente`, ordem 60), sem item na coluna lateral.
- `AbaAtendimentos.tsx`: a lista das consultas do paciente de hoje em diante, com procedimento previsto, profissional e situação.
- `BotaoIniciarAtendimento.tsx`: chama `mudarSituacao(id, "em-atendimento")` e navega; se a agenda recusar, mostra o erro num aviso.
- `TelaDoAtendimento.tsx`: a consulta que está sendo atendida; consulta que não existe mostra o aviso e o caminho de volta.
- `consultas.ts` (+ teste): `consultasAPartirDe` (filtra por paciente e por dia, ordena por horário) e `faixaDeHoras`.

## 🕵️ Dado pessoal (LGPD)

Lê do repositório local a consulta, o nome do paciente e do profissional; a única coisa gravada é a situação da consulta.

## 🧠 Decisões técnicas

- O botão só aparece onde `podeTransitar(situacao, "em-atendimento")` vale (agendada e confirmada): a regra continua sendo da agenda, e quem grava (`mudarSituacao`) confere de novo.
- Abrir `/atendimento/:consultaId` não muda a situação: só o botão muda, para que recarregar a página ou colar o endereço nunca inicie um atendimento sozinho.
- A consulta cancelada aparece na aba, com a situação escrita (`ponytail:` em `consultas.ts` diz onde filtrar se a clínica achar ruído).
- `mudarSituacao` não confere a data (já anotado pela agenda): dá para iniciar uma consulta de outro dia.

## 🧪 Como testar

1. Abra a ficha de um paciente que tenha consulta marcada e vá à aba **Atendimentos**: aparecem só as consultas dele de hoje em diante, por ordem de horário, cada uma com a situação.
2. Numa consulta agendada ou confirmada, clique em **Iniciar atendimento**: a tela `/atendimento/<id>` abre e mostra a situação **Em atendimento**.
3. Volte à ficha: a consulta mostra **Abrir atendimento** no lugar do botão de iniciar.
4. Consulta concluída, com falta ou cancelada não oferece botão nenhum.
5. Abra `/atendimento/inexistente`: aparece **Atendimento não encontrado**, com o caminho de volta à agenda.

## 📎 Documentação afetada

- [[FichaDoPaciente]]
- [[DetalheDaConsulta]]
- [[AtendimentoDaConsulta]]
- [[ADR-003-modulos-por-pasta-com-registro-automatico]]
- [[2026]] (changelog)
