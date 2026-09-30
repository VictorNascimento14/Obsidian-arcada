---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 163
url: https://github.com/VictorNascimento14/Arcada/pull/163
branch: feat/documentos-atestado
tags: [pr, documentos, atestado, impressao]
status: merged
---

# PR #163 — feat(documentos): imprimir o atestado com período e finalidade em texto livre

## 🎯 Contexto

Item 12.3 (Atestado) do [[2026-09-30-plano-da-v1]]. Segundo documento da tela criada no item 12.2 ([[Receituario]], PR #154), sobre a [[FolhaImpressa]] do 12.1 (PR #141); reusa `PacienteEProfissional` e `validacao.ts` do módulo. Fecha a issue #158.

## 🔧 Mudanças

- `src/modulos/documentos/Atestado.tsx`: o cartão do atestado (paciente, profissional, início e fim com data e hora, finalidade, Imprimir) e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/atestado.ts` (+ teste): a regra pura, `prepararAtestado`, que confere o formulário e devolve o que vai para o papel, e `textoDoPeriodo`.
- `src/modulos/documentos/validacao.ts` (+ teste): `horaValida`, que a declaração de comparecimento também vai usar.
- `src/modulos/documentos/PaginaDocumentos.tsx` (+ teste): o cartão entra abaixo do receituário, e a página confere os dois formulários nomeados.
- `src/modulos/documentos/Receituario.tsx`: só o `aria-labelledby` do formulário, para o leitor de tela distinguir os dois cartões.

## 🕵️ Dado pessoal (LGPD)

O atestado leva o nome do paciente e a finalidade (texto livre, que pode ser dado de saúde) para o papel, que sai do controle do app. Na folha entram só o nome do paciente, o período, a finalidade e o nome e o CRO de quem assina, sem CPF nem telefone (o teste confere). Nada é gravado: vive no estado da tela e na folha durante a impressão. Sem validade jurídica, como todo documento da v1.

## 🧠 Decisões técnicas

- **O app não escreve o atestado.** A folha só tem campos (`Paciente`, `Período`, `Finalidade`): nenhuma frase pronta ("Atesto que…"), nenhum prazo de afastamento e nenhum "para os devidos fins de direito", que declarariam um fato e uma validade que só quem emite pode decidir. As horas e a finalidade abrem vazias.
- **Período sempre com hora**, como pede o item (data/hora): `Data de início`, `Hora de início`, `Data de fim` e `Hora de fim`. `textoDoPeriodo` escreve `30/09/2026, das 08:00 às 12:00` no mesmo dia e `de 30/09/2026 às 08:00 até 02/10/2026 às 18:00` em dias diferentes. As datas abrem em hoje (`diaISO`, dia local).
- **O fim tem de ser depois do início** (igual não vale). Compara `AAAA-MM-DDTHH:mm` como texto, que ordena como o calendário. O erro vai para onde quem digitou errou: a hora de fim no mesmo dia, a data de fim em dias diferentes.
- **Finalidade em texto livre, até 500 caracteres** (`LIMITE_DA_FINALIDADE`): uma frase ou duas. O que passar disso é texto de outro documento.
- **`horaValida` mora em `validacao.ts`**, ao lado de `dataValida`: a agenda tem a sua, privada, e a declaração de comparecimento também precisa validar hora.
- **Cada cartão é um formulário com nome** (`aria-labelledby` no `h2`): com dois `Paciente` e dois `Profissional` na tela, o leitor de tela passa a distinguir o do receituário do do atestado.
- **O esqueleto do cartão (título, alerta para leitor de tela, botão) repete o do receituário.** Com o terceiro documento, a declaração, vale extrair um cartão comum; fora deste item.

## ⚠️ Armadilhas e aprendizados

- Uma mensagem de erro que é a única do formulário aparece duas vezes na tela: no campo e no alerta para leitor de tela, que junta todas. `getByText` acha os dois; o teste consulta o `role=alert`.
- Em dias diferentes, comparar só a hora de fim com a de início daria erro falso (`18:00` depois de `08:00` de outro dia): a comparação é do `DataHoraISO` inteiro.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/documentos` — a regra (`prepararAtestado`, `textoDoPeriodo`, `horaValida`), o cartão (abre em branco, erros por campo, o fim antes do início no campo certo, folha no `<body>` no modo do kit, período de vários dias, limpeza no `afterprint`) e a página com os dois formulários nomeados.
2. `npm run dev`, **Documentos** na coluna, cartão **Atestado**: escolher paciente e profissional, informar as horas de início e de fim, escrever a finalidade e clicar em **Imprimir**. Sai só a folha: cabeçalho da clínica, `Período: …`, a finalidade e a linha de assinatura com nome e CRO, em preto sobre branco também com o tema escuro.
3. Com o fim igual ou antes do início, o erro aparece na hora de fim (mesmo dia) ou na data de fim (dias diferentes), e a impressão não abre.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[Atestado]]
- [[Receituario]]
- [[FolhaImpressa]]
- [[2026]] (changelog)
