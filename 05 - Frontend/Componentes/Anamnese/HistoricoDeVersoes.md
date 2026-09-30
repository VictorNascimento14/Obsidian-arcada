---
tipo: funcionalidade
camada: frontend
area: Anamnese
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, anamnese, historico, versoes]
---

# Histórico de versões da anamnese

## O que é

A lista das versões da anamnese de um paciente, no fim da aba **Anamnese** da [[FichaDoPaciente]], e a leitura
das respostas de cada uma. Cada salvamento do [[FormularioAnamnese]] grava uma versão datada; aqui elas se
consultam (item 2.5 do [[2026-09-30-plano-da-v1]]). O histórico é só de leitura: nada se edita nem se apaga.

## Onde está no código

- `src/modulos/anamnese/HistoricoDeVersoes.tsx` — a lista e o diálogo (exportação padrão, recebe `pacienteId`).
- `src/modulos/anamnese/RespostasDaAnamnese.tsx` — as respostas de uma versão, seção por seção, só para ler.
- `src/modulos/anamnese/dados.ts` — `anamneses` e `versoesDoPaciente`, que ordena as versões.
- `src/modulos/anamnese/FormularioAnamnese.tsx` — a aba: o formulário e, abaixo, este histórico.

## Comportamento

- **Uma linha por versão**, da mais nova à mais antiga: `Versão N`, a data (`DD/MM/AAAA`) e, só na mais nova,
  o marcador `Vigente`. A versão 1 é a mais antiga; o número sai da posição e distingue duas versões do mesmo
  dia. No mesmo dia, a última gravada vem primeiro.
- **`Ver respostas`** abre um diálogo (`Versão N · DD/MM/AAAA`) com as respostas daquela versão: cada seção, cada
  pergunta e o que foi respondido — `Sim`, `Sim — detalhe`, `Não` ou o texto, com as quebras de linha como foram
  escritas. O diálogo não tem campo nem botão de edição. Escape, o `Fechar` e o clique fora o fecham, e o foco
  volta ao botão que o abriu.
- **Pergunta sem resposta** aparece como `Não respondida` e texto em branco como `Não informado`: a falta
  nunca é lida como `Não`.
- **Sem versão gravada**, o histórico não aparece (o cabeçalho da aba já diz `Nenhuma anamnese registrada.`).
- **Acompanha a coleção**: cada versão gravada entra na lista, e a vigente passa a ser a nova.
- **O rascunho do formulário não é tocado** ao abrir ou fechar o diálogo.

## Movimento e micro-interações

O diálogo é o `Modal` do kit (sobe com a animação de entrada do vidro, fecha no Escape). O botão tem o toque do
`press`. Nada mais anima.

## Limites conhecidos

- **A lista não pagina**: uma versão por salvamento cresce devagar; se crescer, mostrar as mais novas e um
  `ver todas` (`ponytail:` no código).
- **Não há como restaurar nem comparar versões**: corrigir uma resposta é salvar de novo pelo formulário, que
  abre com a vigente.
- **Salvar sem mudar nada grava uma versão igual à anterior**: aparece na lista como qualquer outra.
- A impressão da anamnese com linha de assinatura é o item 2.6.

## Histórico de mudanças

- [[2026-09-30-pr-122-anamnese-historico]] — a lista de versões e a leitura das respostas de cada uma.
