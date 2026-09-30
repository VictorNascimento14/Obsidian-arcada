---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 122
url: https://github.com/VictorNascimento14/Arcada/pull/122
branch: feat/anamnese-historico
tags: [pr, anamnese, historico, versoes]
status: aberto
---

# PR #122 — feat(anamnese): listar as versões da anamnese e abrir as respostas de cada uma

## 🎯 Contexto

Item 2.5 (Histórico de versões) do [[2026-09-30-plano-da-v1]]. Usa a coleção `anamneses` e `versoesDoPaciente` do item 2.2 (PR #104). `RespostasDaAnamnese`, a leitura das respostas, é a peça que a impressão (item 2.6) reaproveita. Fecha a issue #113.

## 🔧 Mudanças

- `src/modulos/anamnese/HistoricoDeVersoes.tsx` (+ teste): a lista de versões e o diálogo de leitura (`Modal` do kit).
- `src/modulos/anamnese/RespostasDaAnamnese.tsx` (+ teste): as respostas de uma versão para ler, seção por seção.
- `src/modulos/anamnese/FormularioAnamnese.tsx` (+ teste): a aba passa a montar o formulário e o histórico; a `key` por paciente subiu para o elemento de fora.

## 🕵️ Dado pessoal (LGPD)

O diálogo mostra dado de saúde de versões antigas, que continuam só no `localStorage`, na coleção `anamneses`, sem envio a lugar nenhum. Nenhuma versão é apagada nem editada: o histórico é só de leitura, e corrigir uma resposta é gravar uma versão nova. Os exemplos dos testes são genéricos ("Penicilina", "Asma leve").

## 🧠 Decisões técnicas

- **Só leitura, em diálogo.** As respostas abrem no `Modal` do kit, e não no lugar do formulário: o formulário é o foco da aba, e o rascunho fica intacto atrás do diálogo. Não há "restaurar" nem editar uma versão antiga — fora do pedido; corrigir é salvar de novo, pelo formulário, que abre com a vigente.
- **O número sai da posição, não do dado.** A versão 1 é a mais antiga: só se acrescenta versão, nunca se tira, então a posição é estável e a coleção não ganha campo. É o número que distingue duas versões do mesmo dia (a data é só o dia).
- **A lista traz todas as versões, inclusive a vigente**, marcada. A do formulário é a vigente; o histórico mostra tudo o que foi gravado.
- **Pergunta sem resposta aparece como "Não respondida", nunca como "Não"** (a regra do questionário: "não respondeu" não é "não"); texto em branco aparece como "Não informado". A leitura aceita qualquer valor, e um registro estragado vira "Não respondida" em vez de derrubar a tela.
- **`RespostasDaAnamnese` separada do histórico**, porque a impressão do item 2.6 precisa das mesmas respostas fora do diálogo.

## ⚠️ Armadilhas e aprendizados

- A aba passou a ter dois irmãos (o formulário e o histórico), e a primeira versão pôs `key={pacienteId}` nos dois. **Duas `key` iguais entre irmãos se confundem**: ao trocar de paciente o React deixou dois formulários na tela, e o teste da troca de paciente pegou. A `key` fica no `div` de fora, que refaz a aba inteira.
- A lista não pagina (`ponytail:` no código): uma versão por salvamento cresce devagar. Se crescer, mostrar as mais novas e um "ver todas".
- Dentro do diálogo os títulos de seção são `h5`: o título do `Modal` do kit é `h4`.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — o histórico (ordem, numeração, vigente, diálogo, reatividade), a leitura das respostas e a aba.
2. `npm run dev`, abrir um paciente e a aba **Anamnese**: responder tudo "Não" e salvar; depois marcar "Sim" em Alergia, com detalhe, e salvar de novo. O histórico mostra "Versão 2" com **Vigente** e "Versão 1".
3. **Ver respostas** na versão 1: o diálogo mostra a alergia como "Não". Escape fecha, e o foco volta ao botão. Na versão 2 a alergia aparece como "Sim — <detalhe>".

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[HistoricoDeVersoes]]
- [[FormularioAnamnese]]
- [[Ficha do paciente]]
- [[glossario]]
- [[2026]] (changelog)
