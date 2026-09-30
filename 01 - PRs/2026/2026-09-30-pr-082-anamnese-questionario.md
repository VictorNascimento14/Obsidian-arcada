---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 82
url: https://github.com/VictorNascimento14/Arcada/pull/82
branch: feat/anamnese-questionario
tags: [pr, anamnese, questionario]
status: aberto
---

# PR #82 — feat(anamnese): definir o questionário da anamnese e a validação das respostas

## 🎯 Contexto

Item 2.1 (Modelo do questionário) do [[2026-09-30-plano-da-v1]], o primeiro do módulo Anamnese. Puro, sem tela e sem `modulo.ts`: a aba na ficha vem com o formulário (2.2). A regra do domínio que orienta o modelo está no `CLAUDE.md` do repositório: o app não sugere conduta, e os alertas só repetem o que foi respondido. Fecha a issue #78.

## 🔧 Mudanças

- `src/modulos/anamnese/questionario.ts` (+ teste): `SECOES` (5 seções, 19 perguntas), `PERGUNTAS` (a lista corrida), os tipos `Pergunta`, `PerguntaId`, `RespostaSimNao` e `Respostas`, os limites `LIMITE_DO_DETALHE` (200) e `LIMITE_DO_TEXTO` (500) e `respostasValidas`.

## 🕵️ Dado pessoal (LGPD)

Este PR não guarda dado de ninguém: só define as perguntas e confere as respostas. As respostas — dado de saúde, sensível pela LGPD — passam a ser gravadas no item 2.2, na coleção do módulo. Os valores dos testes são exemplos genéricos (“Penicilina”, “Látex”) e nenhum identifica uma pessoa.

## 🧠 Decisões técnicas

- **O id da pergunta é o que se guarda.** Como nas condições do odontograma, a lista é fechada e o `id` (camelCase, sem acento) é o que fica no dado; o rótulo pode ser reescrito sem tocar no que já foi respondido. O teste confere que os ids são únicos e sem acento.
- **`Respostas` é tipado por pergunta.** `PerguntaId` sai da própria lista (`as const satisfies`) e cada chave tem o tipo da sua pergunta: `{ sim, detalhe? }` na sim/não, texto na de texto. O item 2.3 lê `respostas.alergia` sem conversão, e um id errado não compila.
- **Só a pergunta que declara `detalhe` aceita detalhe.** O campo `detalhe` da pergunta é o rótulo do campo (“Qual?”, “A quê?”). Diabetes, pressão alta e gestação não têm: a resposta é só sim ou não.
- **Toda pergunta sim/não precisa de resposta para gravar; a de texto pode faltar.** “Não respondeu” não é “não”, e o alerta nasce do “sim”: uma ficha em branco não pode passar por uma ficha sem alertas.
- **Chave que o questionário não tem invalida o conjunto.** Seria uma resposta que os alertas nunca veriam; melhor recusar do que gravar e ignorar.
- **Detalhe junto de um “não” é aceito.** Descartá-lo ao mudar a resposta é da tela, e ignorá-lo quando `sim` é falso é de quem lê; a regra não recusa o dado que só sobrou.
- **`respostasValidas` aceita `unknown` e devolve `boolean`**, como `denteValido`: serve a dado guardado ou digitado. Os limites (200 caracteres no detalhe, 500 no texto) são exportados para o formulário do item 2.2 usar os mesmos números.

## ⚠️ Armadilhas e aprendizados

- `PerguntaId` e `Respostas` só ficam estreitos porque a lista é `as const satisfies readonly Secao[]`. Trocar por uma anotação (`: Secao[]`) alarga o id para `string`, e `Respostas` passa a aceitar qualquer chave sem erro nenhum.
- Um predicado de tipo (`respostas is Respostas`) estreitaria o ramo falso para `never` quando quem chama já tem o valor tipado como `Respostas` — o mesmo motivo de `denteValido` devolver `boolean`.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — a estrutura do questionário (seções, ids únicos e sem acento) e `respostasValidas`: respostas mínimas, pergunta sim/não sem resposta, pergunta que não existe, resposta de outro tipo, detalhe onde a pergunta não tem campo, limites de tamanho e valor que não é um conjunto de respostas.
2. `npm run type-check` — o teste traz um `@ts-expect-error` que prova que o tipo `Respostas` não aceita resposta sim/não numa pergunta de texto.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[glossario]]
- [[2026]] (changelog)
