---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 104
url: https://github.com/VictorNascimento14/Arcada/pull/104
branch: feat/anamnese-formulario
tags: [pr, anamnese, formulario, ficha]
status: aberto
---

# PR #104 — feat(anamnese): preencher e salvar a anamnese na ficha do paciente

## 🎯 Contexto

Item 2.2 (Formulário na ficha) do [[2026-09-30-plano-da-v1]]. Usa o modelo do item 2.1 (PR #82) e a aba que a ficha do paciente lê de `NAVEGACAO.abasPaciente` (PR #76). O selo e o histórico dependem da coleção criada aqui e vêm nos itens 2.4 e 2.5. Fecha a issue #95.

## 🔧 Mudanças

- `src/modulos/anamnese/dados.ts` (+ teste): a coleção `anamneses` (`Anamnese`: `id`, `pacienteId`, `data`, `respostas`), `salvarAnamnese(pacienteId, respostas)` e `versoesDoPaciente(todas, pacienteId)`, da versão mais nova à mais antiga.
- `src/modulos/anamnese/FormularioAnamnese.tsx` (+ teste): o formulário, sem rota; é o `Componente` da aba.
- `src/modulos/anamnese/modulo.ts` (+ teste): só a `abaPaciente` (ordem 10, rótulo "Anamnese"), sem rota e sem item na coluna.

## 🕵️ Dado pessoal (LGPD)

A anamnese é dado de saúde, sensível pela LGPD. Fica só no `localStorage` do navegador, na coleção `anamneses` de `src/dados/`, sem envio a lugar nenhum. O formulário usa `autoComplete="off"`, para o navegador não guardar nem oferecer o que foi respondido sobre um paciente. Os exemplos dos testes são genéricos ("Penicilina", "Látex") e os pacientes, fictícios e sem CPF.

## 🧠 Decisões técnicas

- **Cada salvamento é uma versão nova, nunca uma edição.** `salvarAnamnese` sempre insere um item com id próprio e a data de hoje por `diaISO` (não `toISOString()`, que à noite já dá o dia seguinte); o teste roda às 23h30. A vigente é a mais recente: por data e, no mesmo dia, a última gravada (a coleção guarda na ordem de entrada e o `sort` é estável). Salvar sem mudar nada também grava uma versão — teto conhecido, e o histórico do item 2.5 mostra as duas.
- **A escrita normaliza e valida; a tela é só conforto.** `salvarAnamnese` apara texto e detalhe, tira o que ficou em branco, deixa de fora o detalhe de um "não" (digitado antes de mudar de ideia) e só então chama `respostasValidas`. Devolve `null`, sem gravar, se o paciente não existe ou as respostas não servem; a tela mostra o aviso em vez de perder a gravação em silêncio.
- **Toda pergunta de sim ou não precisa de resposta**, como manda o questionário ("não respondeu" não é "não"). O formulário abre sem nenhuma marcada; ao tentar salvar, cada pergunta em aberto mostra "Responda sim ou não." e o foco vai à primeira, na ordem da tela.
- **Sim/Não são radios nativos** escondidos atrás de uma pílula (`sr-only` + `peer`): teclado (setas) e leitor de tela ficam com o controle do navegador, e o foco aparece na pílula. Cada pergunta é um `fieldset` com o texto dela na `legend`, e o campo de detalhe entra dentro dele, então o "Qual?" que se repete em três perguntas é lido com o nome da pergunta.
- **O formulário tem `key` por paciente.** A ficha reaproveita a aba ao trocar de paciente na mesma rota, e sem a `key` o rascunho de um apareceria na tela do outro; há teste.
- **A data se mostra por `dataBR`**, de `src/modulos/pacientes/exibicao.ts` (importada, não copiada): `new Date("2026-09-30")` leria UTC e recuaria um dia.

## ⚠️ Armadilhas e aprendizados

- O rascunho vive no componente e a ficha monta só a aba ativa: **trocar de aba antes de salvar descarta o que foi digitado**. Marcado no código com `ponytail:`; se doer, guardar o rascunho por paciente fora do componente.
- `Respostas` é indexado por `PerguntaId` (literais), mas `Pergunta.id` é `string`: o rascunho da tela é um `Record<string, …>` e só `salvarAnamnese` — que valida — devolve o tipo `Respostas`. O `as Respostas` fica em um lugar só, logo antes de `respostasValidas` confirmar o conteúdo.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — as regras de gravação (`dados.test.ts`), a tela (`FormularioAnamnese.test.tsx`) e o registro da aba (`modulo.test.ts`).
2. `npm run dev`, abrir um paciente em `/pacientes` e escolher a aba **Anamnese**: tentar salvar sem responder mostra "Responda sim ou não." nas perguntas em aberto e leva o foco à primeira.
3. Responder tudo (com um "sim" em Alergia e o detalhe preenchido) e salvar: aparece o aviso "Anamnese salva" e o cabeçalho passa a dizer a data da última versão. Salvar de novo grava outra versão (conferir em `localStorage`, chave `arcada:anamneses`).

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[FormularioAnamnese]]
- [[FichaDoPaciente]]
- [[glossario]]
- [[2026]] (changelog)
