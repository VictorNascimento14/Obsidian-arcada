---
tipo: funcionalidade
camada: frontend
area: Atendimento
rota: /atendimento/:consultaId
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, atendimento, evolucao, lgpd]
---

# Evolução clínica

## O que é

O cartão da tela do [[AtendimentoDaConsulta]] onde o profissional escreve, em texto livre, o que aconteceu na consulta.
Cada evolução fica com o texto, o dia, o profissional e a consulta. Só aparece com a consulta em atendimento.

**O Arcada não escreve a evolução**: não há modelo pré-preenchido, texto sugerido nem conduta. O campo nasce vazio e o
texto é do profissional. É a mesma regra do domínio que vale para os alertas da anamnese: o app repete o que foi
registrado, nunca decide tratamento.

## Onde está no código

- `src/modulos/atendimento/EvolucaoClinica.tsx` — o cartão.
- `src/modulos/atendimento/dados.ts` — o tipo `Evolucao`, a coleção `evolucoes` (dado só deste módulo) e
  `registrarEvolucao`.
- `TelaDoAtendimento.tsx` — monta o cartão quando a consulta está `Em atendimento`.

## Comportamento

- Um campo de texto (`Evolução`) **vazio, sem `placeholder`**, e o botão `Registrar evolução`, desabilitado enquanto o
  campo estiver em branco.
- Registrar grava a evolução, limpa o campo e a põe no alto da lista. A lista mostra **só as evoluções desta consulta**,
  da mais recente à mais antiga, cada uma com `DD/MM/AAAA · profissional` e o texto, com as quebras de linha como foram
  digitadas.
- O **profissional é o da consulta** e o **dia é o de hoje** (`diaISO`). A evolução guarda também o paciente, para a
  linha do tempo do paciente não precisar passar pela consulta.
- `registrarEvolucao` valida na gravação: a consulta existe e está `Em atendimento`; o texto, aparado só nas pontas
  (as quebras de linha do meio ficam), não está vazio e tem no máximo **5.000 caracteres**. A recusa sai num aviso, e o
  texto digitado continua no campo. Não há `maxLength` no campo: colar um texto maior avisa em vez de cortar em
  silêncio um registro clínico.
- **A evolução só se acrescenta**: não há editar nem apagar. É registro clínico, e corrigir é registrar outra evolução.

## Dado pessoal (LGPD)

A evolução é dado de saúde: dado pessoal sensível. Fica só no repositório local (coleção `evolucoes`), sem sair do
navegador. O texto nunca é copiado para aviso, log ou mensagem de erro.

## Histórico de mudanças

- [[2026-09-30-pr-152-atendimento-evolucao]] — o cartão e a coleção `evolucoes`, com o registro da evolução da consulta.
