---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 152
url: https://github.com/VictorNascimento14/Arcada/pull/152
branch: feat/atendimento-evolucao
tags: [pr, atendimento, evolucao, lgpd]
status: aberto
---

# PR #152 — feat(atendimento): registrar a evolução clínica da consulta

## 🎯 Contexto

Item 9.3 (Evolução clínica) do [[2026-09-30-plano-da-v1]]: texto livre com data e profissional, na coleção do módulo `evolucoes`; sem conduta sugerida e sem modelo pré-preenchido de diagnóstico. Fecha a issue #148.

## 🔧 Mudanças

- `src/modulos/atendimento/dados.ts` (+ teste): o tipo `Evolucao`, a coleção `evolucoes` e `registrarEvolucao(consultaId, texto)`, que valida e grava.
- `EvolucaoClinica.tsx` (+ teste): o cartão com o campo, o botão e a lista da consulta.
- `TelaDoAtendimento.tsx`: mostra o cartão quando a consulta está em atendimento.

## 🕵️ Dado pessoal (LGPD)

A evolução é dado de saúde do paciente, guardado só no repositório local (`evolucoes`), sem sair do navegador. O texto entra inteiro e nunca é copiado para aviso, log ou mensagem de erro.

## 🧠 Decisões técnicas

- Sem `placeholder`, sem texto inicial e sem lista de frases: o teste do cartão confere que o campo nasce vazio. Nenhuma regra do app escreve, sugere ou decide conduta.
- A escrita valida: consulta que existe e está em atendimento, texto aparado (só as pontas; as quebras de linha do meio ficam) não vazio e com no máximo 5.000 caracteres. Sem `maxLength` no campo: colar um texto maior avisa em vez de cortar em silêncio um registro clínico.
- O profissional é o da consulta e o dia é o de hoje (`diaISO`), como a anamnese datada; a evolução guarda também o paciente, para a linha do tempo do paciente (9.5) não precisar passar pela consulta.
- A evolução só se acrescenta: não há editar nem apagar, porque é registro clínico e a correção é uma evolução nova (`ponytail:` em `dados.ts` nomeia o teto e o caminho de upgrade).

## 🧪 Como testar

1. Inicie o atendimento de uma consulta (aba Atendimentos da ficha): a tela mostra o cartão **Evolução clínica** com o campo vazio e o botão desabilitado.
2. Escreva um texto com mais de uma linha e clique em **Registrar evolução**: o campo limpa e a evolução aparece na lista com o dia e o nome do profissional, com as quebras de linha preservadas.
3. Registre uma segunda: ela aparece acima da primeira, e a primeira continua lá.
4. Um texto só de espaços não habilita o botão; um texto com mais de 5.000 caracteres é recusado com um aviso.
5. Com a consulta agendada ou já concluída, o cartão não aparece.

## 📎 Documentação afetada

- [[AtendimentoDaConsulta]]
- [[EvolucaoClinica]]
- [[2026]] (changelog)
