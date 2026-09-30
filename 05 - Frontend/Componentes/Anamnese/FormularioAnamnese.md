---
tipo: funcionalidade
camada: frontend
area: Anamnese
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, anamnese, formulario]
---

# Formulário de anamnese

## O que é

A aba **Anamnese** da [[FichaDoPaciente]]: as perguntas do questionário para preencher com o paciente e salvar.
É o item 2.2 do [[2026-09-30-plano-da-v1]]. Não tem rota própria: o módulo só registra a aba (`abaPaciente`,
ordem 10), e a ficha a monta quando ela é escolhida. As perguntas e o que significa cada resposta estão no
[[glossario]] (Anamnese); o modelo é o do PR [[2026-09-30-pr-082-anamnese-questionario]].

## Onde está no código

- `src/modulos/anamnese/modulo.ts` — só a `abaPaciente`: ordem 10, rótulo `Anamnese`.
- `src/modulos/anamnese/FormularioAnamnese.tsx` — a tela (exportação padrão, recebe `pacienteId`).
- `src/modulos/anamnese/dados.ts` — a coleção `anamneses`, `salvarAnamnese` e `versoesDoPaciente`.
- `src/modulos/anamnese/questionario.ts` — as seções, as perguntas e `respostasValidas`.
- `src/modulos/anamnese/SeloAlertas.tsx` e `HistoricoDeVersoes.tsx` — montados por esta tela: o selo no topo da
  aba e o histórico abaixo do formulário ([[SeloAlertas]], [[HistoricoDeVersoes]]).

## Comportamento

- **Cabeçalho**: `Última versão: DD/MM/AAAA.` ou `Nenhuma anamnese registrada.`, e o lembrete de que cada
  salvamento grava uma nova versão com a data de hoje.
- **Seções**: cinco, na ordem do questionário, cada uma com o título e as perguntas em duas colunas a partir de
  `md` (uma coluna no celular).
- **Pergunta de sim ou não**: um grupo (`fieldset`, com a pergunta na `legend`) com as opções `Sim` e `Não`.
  Escolher `Sim` numa pergunta que tem detalhe mostra o campo (`Qual?`, `A quê?`…); voltar a `Não` esconde o
  campo, e o que foi digitado nele não é gravado.
- **Pergunta de texto**: um campo de uma linha, com o limite do questionário.
- **Abrir**: sem versão anterior, nada vem marcado. Com versão, o formulário abre com as respostas da mais
  recente — salvar de novo cria a versão seguinte, e a anterior fica como estava.
- **Salvar anamnese**: se alguma pergunta de sim ou não está sem resposta, não grava; cada uma mostra
  `Responda sim ou não.`, o foco vai à primeira e um aviso para leitor de tela diz quantas faltam. Com tudo
  respondido, grava e mostra o aviso `Anamnese salva` (`Versão de DD/MM/AAAA.`).
- **Se a gravação for recusada** (por exemplo, o paciente foi removido em outra aba), aparece `Não foi possível
  salvar a anamnese` e nada é gravado.
- **Cada paciente tem o seu formulário**: trocar de paciente na mesma tela não leva o que foi digitado.
- **Selo de alertas**: no topo da aba, o [[SeloAlertas]] mostra os alertas da última versão **salva**; o que
  está só no rascunho do formulário não entra.
- **Histórico e impressão**: abaixo do formulário, o [[HistoricoDeVersoes]] lista as versões salvas e abre as
  respostas de cada uma; o botão **Imprimir** de cada linha abre a [[ImpressaoDaAnamnese]].
- **Sem sugestão de conduta**: a tela só colhe respostas; os alertas derivados delas só repetem o que foi
  respondido.

## Movimento e micro-interações

`Sim` e `Não` são pílulas com o toque do `press` do kit; a marcada fica em `primary-900`. O foco por teclado é o
contorno da pílula (`peer-focus-visible`), e as setas movem entre `Sim` e `Não` do mesmo grupo, porque são radios
nativos. Nada mais anima.

## Limites conhecidos

- **O que não foi salvo se perde ao trocar de aba**: a ficha monta só a aba ativa, e o rascunho vive no
  componente.
- **Salvar sem mudar nada grava uma versão igual à anterior**: cada salvamento é uma versão.
## Histórico de mudanças

- [[2026-09-30-pr-104-anamnese-formulario]] — o formulário na ficha, com gravação de versões datadas.
- [[2026-09-30-pr-111-anamnese-selo]] — o selo de alertas no topo da aba.
- [[2026-09-30-pr-122-anamnese-historico]] — o histórico de versões abaixo do formulário.
- [[2026-09-30-pr-131-anamnese-impressao]] — a impressão de uma versão, pelo botão Imprimir do histórico.
