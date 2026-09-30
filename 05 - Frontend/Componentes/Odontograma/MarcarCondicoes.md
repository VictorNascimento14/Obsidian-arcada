---
tipo: funcionalidade
camada: frontend
area: Odontograma
rota: /pacientes/:id (aba "Odontograma")
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, odontograma, condicoes, ficha]
---

# Marcar condição por face e por dente

## O que é

A aba **Odontograma** da ficha do paciente: uma barra escolhe a condição e o [[Arcadas]] é onde ela se marca. Clicar
numa face aplica a condição escolhida ou a remove; as condições do dente inteiro se marcam pelo número do dente.
As marcas ficam guardadas por paciente e voltam ao reabrir a ficha. É o item 3.7 do [[2026-09-30-plano-da-v1]]. As
condições são as de [[CondicoesELegenda]]; o app só registra o que o profissional marcou e não sugere conduta.

## Onde está no código

- `src/modulos/odontograma/modulo.ts` — só a `abaPaciente` (ordem 20, rótulo "Odontograma"; a de Tratamentos é 40).
  O módulo não tem rota nem item de coluna.
- `src/modulos/odontograma/AbaOdontograma.tsx` — a aba: guarda a condição escolhida e liga a barra, o
  odontograma e os dados.
- `src/modulos/odontograma/BarraDeCondicoes.tsx` — a barra que escolhe a condição.
- `src/modulos/odontograma/marcas.ts` — a regra pura: `alternarMarca` (liga e desliga) e `conferirMarca` (valida).
- `src/modulos/odontograma/dados.ts` — a coleção `odontogramas`, o hook `useMarcas` e `alternarMarcaDoPaciente`.
- `src/modulos/odontograma/desenho.tsx` — a forma das faces, a marca de cada condição e o `IconeDaCondicao`.
- `Dente.tsx`, `Odontograma.tsx`, `Legenda.tsx` e `condicoes.ts` ([[Dente]], [[Arcadas]], [[CondicoesELegenda]])
  ganharam o que a marcação pede; os testes de todos estão ao lado.

## Comportamento

- **A barra** tem as nove condições em dois grupos, "Por face" e "Dente inteiro", como `radio` nativos (uma só vale
  por vez; as setas trocam). Abre em Cárie. A frase de baixo diz onde clicar: "Clique numa face do dente…" para as
  condições de face e "Clique no número do dente…" para as do dente inteiro.
- **Condição de face** (cárie, restauração, selante): clicar na face, ou `Enter` e `Espaço` nela, a aplica; clicar
  de novo a remove. Uma face tem no máximo uma condição, então aplicar outra a substitui. O número do dente fica
  só como rótulo.
- **Condição de dente inteiro** (fratura, extração indicada, ausente, tratamento de canal, coroa, implante):
  clicar no número do dente a liga ou desliga, cada uma por si — o dente pode ter várias (tratamento de canal e
  coroa, por exemplo). Enquanto uma delas está escolhida, as faces ficam `aria-disabled` e não marcam.
- **Cada condição tem um símbolo só seu, além da cor.** Ele aparece no dente, na barra e na `Legenda`:

  | Condição | Vale em | Símbolo |
  |---|---|---|
  | Cárie | face | círculo cheio no meio da face, com a face tingida |
  | Restauração | face | quadrado cheio, com a face tingida |
  | Selante | face | anel (círculo vazado), com a face tingida |
  | Fratura | dente | traço em zigue-zague de ponta a ponta |
  | Extração indicada | dente | X sobre o dente |
  | Ausente | dente | contorno tracejado com um traço horizontal no meio |
  | Tratamento de canal | dente | linha vertical no meio |
  | Coroa | dente | anel em volta do dente |
  | Implante | dente | haste vertical com três travessas (a rosca) |

- **Acessibilidade.** O nome da face traz a condição (`face mesial do dente 16: cárie`) e o do desenho lista as do
  dente (`Dente 16, primeiro molar superior direito. Condições do dente: tratamento de canal, coroa`). Depois de cada
  clique, uma região `role="status"` (só para leitor de tela) diz o resultado: `Marcado: cárie na face mesial do
  dente 16.` ou `Desmarcado: coroa no dente 36.` O nome de um elemento que muda sozinho não é lido por todos os
  leitores.
- **Guardado por paciente**: uma entrada da coleção `odontogramas` para cada paciente (o `id` é o do paciente), com
  a lista de marcas. Sem marcas, a lista é vazia; nenhuma semente foi acrescentada.

## Modelo de dados

Uma marca é `{ dente, face?, condicao }`: `{ dente: 16, face: "M", condicao: "carie" }` numa face e
`{ dente: 16, condicao: "coroa" }` no dente inteiro. A entrada guardada é `{ id: <pacienteId>, marcas: [...] }`.
Uma lista plana (e não um mapa por dente) casa com uma tabela de um backend e deixa simples o que vem depois:
limpar um dente (3.8), fixar o estado inicial (3.9) e contar por condição (3.10). A escrita valida (`conferirMarca`):
dente que existe, condição da lista, face que o dente tem e face só onde a condição é de face.

## Movimento e micro-interações

A pílula escolhida na barra ganha contorno e realce (a diferença é de forma, não só de cor); o foco por teclado
desenha o anel na pílula. Nada anima além do realce das faces ao passar o mouse.

## Limites conhecidos

- **Nada apaga o odontograma quando o paciente é excluído** (o módulo de pacientes ainda não exclui). É dado de
  saúde: quem fizer a exclusão precisa remover a entrada de `odontogramas` do paciente.
- **160 paradas de `Tab` nas faces** e, com condição de dente inteiro escolhida, mais 32 nos números. Setas entre
  os dentes ficaram como melhoria.
- **Uma condição por face**: não dá para registrar, por exemplo, restauração e cárie na mesma face. Se a clínica
  precisar, a lista plana comporta a mudança (a face passa a aceitar várias marcas).
- **A condição escolhida não é guardada**: a aba abre sempre em Cárie.

## Histórico de mudanças

- [[2026-09-30-pr-112-odontograma-marcar]] — a aba, a barra de condições, a marcação por face e por dente, os símbolos e a coleção `odontogramas`.
