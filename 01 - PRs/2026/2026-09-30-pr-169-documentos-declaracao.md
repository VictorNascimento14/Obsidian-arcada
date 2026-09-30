---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 169
url: https://github.com/VictorNascimento14/Arcada/pull/169
branch: feat/documentos-declaracao
tags: [pr, documentos, declaracao, impressao]
status: aberto
---

# PR #169 — feat(documentos): imprimir a declaração de comparecimento com o horário da consulta

## 🎯 Contexto

Item 12.4 (Declaração de comparecimento) do [[2026-09-30-plano-da-v1]]. Terceiro documento da tela criada no item 12.2 ([[Receituario]], PR #154), depois do [[Atestado]] (PR #163), sobre a [[FolhaImpressa]] (PR #141). Lê as consultas da agenda (`consultas`, coleção do núcleo) e reusa `emMinutos`/`emHora` e `ROTULO_DA_SITUACAO` do módulo da agenda. Fecha a issue #166.

## 🔧 Mudanças

- `src/modulos/documentos/Declaracao.tsx`: o cartão da declaração (paciente, profissional, consulta do paciente, data, horas, Imprimir) e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/declaracao.ts` (+ teste): a regra pura, com `consultasDoPaciente`, `horarioDaConsulta` e `prepararDeclaracao`.
- `src/modulos/documentos/PaginaDocumentos.tsx` (+ teste): o cartão entra abaixo do atestado, e a página confere os três formulários nomeados.
- `src/modulos/documentos/Declaracao.test.tsx`: 8 testes de tela.

## 🕵️ Dado pessoal (LGPD)

A declaração leva para o papel o nome do paciente e o dia e o horário em que esteve na clínica, que sai do controle do app. Entram só o nome do paciente, o dia e o horário e o nome e o CRO de quem assina: sem CPF, sem telefone e sem a situação da consulta nem o procedimento (o teste confere). Nada é gravado: vive no estado da tela e na folha durante a impressão. Sem validade jurídica, como todo documento da v1.

## 🧠 Decisões técnicas

- **A frase é a única coisa que o app escreve.** `Declaro que <nome> compareceu a esta clínica no dia <data>, das <início> às <fim>.` é composta só do que veio dos campos, sem "para os devidos fins de direito" nem qualquer menção de validade. Ao contrário do atestado (só campos), aqui a frase é o próprio documento, e quem assina a lê antes de assinar.
- **A consulta é só um atalho de preenchimento.** `consultaId` vive no estado da tela e não vai para `prepararDeclaracao` nem para o papel. Escolher a consulta copia a data, o início e o fim (início mais a duração) para os campos, que seguem editáveis. Um fim que passaria da meia-noite vira 23:59: a declaração é de um dia só.
- **Que consultas entram.** `consultasDoPaciente` tira `faltou` e `cancelada` (o paciente não compareceu, ou não houve consulta) e deixa agendada, confirmada, em atendimento e concluída, da mais recente para a mais antiga. A agenda pode estar atrás do consultório (a recepção ainda não iniciou o atendimento), e quem assina é quem decide; por isso cada opção mostra a situação.
- **Trocar de paciente solta a consulta escolhida**, que era de outro; a data e as horas já preenchidas ficam como estão.
- **Reuso em vez de conta nova**: `emMinutos` e `emHora` (agenda/horarios), `ROTULO_DA_SITUACAO` (agenda/situacao), `textoDoPeriodo` (atestado.ts), `PacienteEProfissional`, `Selecao` e `validacao.ts`.
- **`text-balance` na frase** (Tailwind 3.4, `text-wrap: balance`): sem ele, o PDF mostrava `16:00.` sozinho na segunda linha.

## ⚠️ Armadilhas e aprendizados

- O esqueleto do cartão (título, alerta para leitor de tela, botão) já se repete em três documentos: a regra dos três está atingida, e um `CartaoDoDocumento` comum é o próximo passo, fora deste item.
- `getByText` não acha uma frase com `<strong>` no meio: o texto está em nós separados. O teste usa um matcher por função sobre o `textContent` do `<p>`.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/documentos` — a regra (`consultasDoPaciente`, `horarioDaConsulta`, `prepararDeclaracao`), o cartão (consultas do paciente, preencher a partir de uma consulta, trocar de paciente, erros por campo, folha no `<body>` no modo do kit, limpeza no `afterprint`) e a página com os três formulários nomeados.
2. `npm run dev`, **Documentos** na coluna, cartão **Declaração de comparecimento**: escolher um paciente com consulta (por exemplo, Ana Beatriz Moura), escolher a consulta na lista (a data e as horas se preenchem), escolher o profissional e clicar em **Imprimir**. Sai só a folha: cabeçalho da clínica, a frase da declaração e a linha de assinatura com nome e CRO, em preto sobre branco também com o tema escuro.
3. Trocar de paciente solta a consulta escolhida. Um paciente sem consulta deixa a lista desabilitada, e o horário se digita. Com o fim igual ou antes do início, o erro aparece na hora de fim e a impressão não abre.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[Declaracao]]
- [[Documentos]]
- [[Atestado]]
- [[Receituario]]
- [[FolhaImpressa]]
- [[2026]] (changelog)
