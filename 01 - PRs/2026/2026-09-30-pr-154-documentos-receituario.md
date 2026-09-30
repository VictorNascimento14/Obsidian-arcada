---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 154
url: https://github.com/VictorNascimento14/Arcada/pull/154
branch: feat/documentos-receituario
tags: [pr, documentos, receituario, impressao]
status: merged
---

# PR #154 — feat(documentos): imprimir o receituário em texto livre com linha para assinatura

## 🎯 Contexto

Item 12.2 (Receituário) do [[2026-09-30-plano-da-v1]]. Primeira tela a usar a [[FolhaImpressa]] do item 12.1 (PR #141); o cabeçalho sai do cadastro da [[Clinica]] e quem assina vem de [[Profissionais]]. Fecha a issue #144.

## 🔧 Mudanças

- `src/modulos/documentos/modulo.ts`: rota `/documentos` e item da coluna (grupo `gestao`, ordem 30, ícone `file`); só o registro do módulo, nenhum arquivo compartilhado editado.
- `src/modulos/documentos/PaginaDocumentos.tsx`: a tela, com o aviso de que a v1 é demonstração e um cartão por documento.
- `src/modulos/documentos/Receituario.tsx`: o cartão do receituário (paciente, profissional, data, texto, Imprimir) e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/receituario.ts` (+ teste): a regra pura, `prepararReceituario`, que confere o formulário e devolve o que vai para o papel.
- `src/modulos/documentos/validacao.ts` (+ teste): `resolverEscolha` (paciente e profissional ativo) e `dataValida`, que atestado e declaração também usam.
- `src/modulos/documentos/PacienteEProfissional.tsx` e `Selecao.tsx`: as duas escolhas de todo documento e o `<select>` do sistema.
- `modulo.test.ts`, `PaginaDocumentos.test.tsx`, `Receituario.test.tsx`: 18 testes no módulo.

## 🕵️ Dado pessoal (LGPD)

O receituário leva o nome do paciente e um texto de saúde para o papel, que sai do controle do app. Na folha entram só o nome do paciente e o nome e o CRO de quem assina, sem CPF nem telefone (o teste confere). O texto não é gravado em lugar nenhum: vive no estado da tela e na folha durante a impressão. Sem validade jurídica, como todo documento da v1.

## 🧠 Decisões técnicas

- **Texto livre de verdade.** O campo abre vazio, sem `placeholder`, sem lista de modelos e sem nenhuma palavra clínica no código; a tela diz que o app não sugere medicamento, dose nem conduta. Limite de 2.000 caracteres (`LIMITE_DO_TEXTO`), do tamanho de uma página de texto corrido.
- **A regra é pura e devolve o retrato do que imprime.** `prepararReceituario` confere tudo (paciente, profissional ativo, data que existe no calendário, texto não vazio e no limite) e, estando certo, entrega só `{ paciente: { nome }, profissional: { nome, cro }, data, texto }`. É o que vai para o `useImpressao`: o que sai no papel não muda se o formulário mudar depois, e nenhum outro campo do cadastro chega à folha.
- **Profissional inativo é regra, não só lista.** A lista da tela mostra só os ativos (`profissionalAtivo`), e `resolverEscolha` também recusa quem foi desativado depois de escolhido. O paciente removido depois de escolhido cai no mesmo erro.
- **`dataValida` reusa `somarDias` da agenda** (`2026-02-30` passa no formato e volta como `2026-03-02`), em vez de uma terceira conta com `Date`.
- **As duas escolhas moram num componente que os próximos documentos reusam** (`PacienteEProfissional`), e o `<select>` (`Selecao`) repete a caixa do de [[MarcarConsulta]], que é privado da agenda: um terceiro uso justifica subir para `src/componentes/`.
- **Cartão por documento, empilhados**, como a tela da clínica; cada documento tem o seu estado, e atestado e declaração entram como novos cartões, sem mexer neste.

## ⚠️ Armadilhas e aprendizados

- O `maxLength` do `<textarea>` só barra a digitação: valor colocado por código passa dele, então a regra também confere o limite. O limite é em caracteres, não em linhas: 70 linhas curtas passam de uma página, e a folha segue para a 2ª levando nome e CRO junto do fim do texto (`break-before-avoid`).
- `<select>` controlado que perde a opção (profissional desativado depois de escolhido) mostra o primeiro item, mas o estado da tela ainda guarda o id: por isso a validação olha os dados, não o que o campo mostra.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/documentos` — a regra (`prepararReceituario`, `resolverEscolha`, `dataValida`), o cartão (abre em branco, erros por campo, folha no `<body>` no modo do kit, limpeza no `afterprint`, imprimir outra via), o módulo e a rota pelo item da coluna.
2. `npm run dev`, abrir **Documentos** na coluna (grupo Gestão), escolher paciente e profissional, escrever um texto e clicar em **Imprimir**. No diálogo (ou em "Salvar como PDF") sai só a folha: cabeçalho da clínica (os dados vêm de **Clínica**), paciente, data, o texto e a linha de assinatura com nome e CRO, em preto sobre branco também com o tema escuro.
3. Clicar em **Imprimir** com o formulário em branco mostra o erro em cada campo e não abre a impressão. Um profissional desativado em **Clínica** não aparece na lista.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[Receituario]]
- [[FolhaImpressa]]
- [[2026]] (changelog)
