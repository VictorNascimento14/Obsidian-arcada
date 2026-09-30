---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 131
url: https://github.com/VictorNascimento14/Arcada/pull/131
branch: feat/anamnese-impressao
tags: [pr, anamnese, impressao, assinatura]
status: aberto
---

# PR #131 — feat(anamnese): imprimir a anamnese com linha para assinatura do paciente

## 🎯 Contexto

Item 2.6 (Impressão com linha de assinatura) do [[2026-09-30-plano-da-v1]]. Usa `RespostasDaAnamnese` do item 2.5 (PR #122) e o CSS de impressão do kit (`src/ui/index.css`, bloco "Exportar PDF"). Fecha a issue #123.

## 🔧 Mudanças

- `src/modulos/anamnese/FolhaDaAnamnese.tsx`: a folha, montada por portal direto no `<body>` com `data-print-clone`.
- `src/modulos/anamnese/HistoricoDeVersoes.tsx` (+ testes): o botão Imprimir de cada versão e o efeito que liga `data-print-mode="clone"`, chama `window.print()` e desfaz no `afterprint`.
- `src/modulos/anamnese/RespostasDaAnamnese.tsx`: `print:text-black`, corpo e espaços menores no papel e seção que não parte entre páginas.

## 🕵️ Dado pessoal (LGPD)

A folha leva dado de saúde e o nome do paciente para o papel, que sai do controle do app. Da ficha entra só o nome — sem CPF nem telefone, e o teste confere. A folha só existe no DOM durante a impressão: não fica uma segunda cópia do dado escondida na página. Sem validade jurídica, como todo documento da v1.

## 🧠 Decisões técnicas

- **O CSS de impressão do kit pede um clone no `<body>`, e o helper que o monta não veio.** As regras `@media print` do kit escondem tudo o que é filho direto do `<body>`, menos o elemento com `data-print-clone`, e só com `data-print-mode="clone"` no `<body>`; na tela o clone fica `display: none`. O `imprimirComoPdf` que o comentário do CSS cita não existe no Arcada, então a folha é montada por portal no `<body>` e o modo é ligado por `document.body.dataset`. Os detalhes estão em [[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]].
- **O botão fica em cada linha do histórico, não no formulário.** Imprime uma versão determinada, sem dúvida entre o que foi salvo e o rascunho; o fluxo é salvar, aparecer a linha nova (Vigente) e imprimir.
- **A folha só existe durante a impressão.** O clique guarda `{ versao, numero }` em estado; a folha é renderizada, o efeito liga o modo, chama `window.print()` e o `afterprint` limpa. Um objeto novo por clique refaz o efeito, e a limpeza do efeito também desliga o modo se a tela sair com a folha no ar.
- **Cor fixa em preto na folha** (`text-black`, `print:text-black` nas respostas): no tema escuro os tokens do app são claros, e claro sobre o papel branco some.
- **Só o nome do paciente no cabeçalho**, com a data e o número da versão; o número distingue duas versões do mesmo dia.
- **Compacta no papel**: corpo e espaços menores para a anamnese inteira caber numa página A4 com a assinatura, e `break-before-avoid` para a linha de assinatura não ficar sozinha numa página. Sem isso, a primeira versão da folha deixava a linha sozinha na segunda página.

## ⚠️ Armadilhas e aprendizados

- **O `afterprint` também vem do `printToPDF` do Chrome.** Quem testar por CDP precisa clicar em Imprimir de novo antes de cada PDF, porque a folha some depois do primeiro: um segundo PDF tirado sem isso mostra a tela inteira, e parece que o CSS do kit falhou.
- **Não remover a folha logo depois do `window.print()`.** Na maioria dos navegadores ele bloqueia até o diálogo fechar, mas em outros (celular) devolve na hora, e a limpeza tem de esperar o `afterprint`.
- **Fundo do tema escuro**: o `<body>` continua escuro. O navegador não imprime fundo por padrão; com "gráficos de segundo plano" ligado, o fundo sairia, e o CSS de impressão do kit não zera o do `<body>` (`src/ui/index.css` não se edita aqui).
- **`StrictMode`**: o efeito roda por mudança de estado, não na montagem, então não abre a impressão duas vezes em desenvolvimento. O `window.print` do jsdom não faz nada útil: os testes o trocam por um espião que anota o que havia na tela na hora da chamada.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — a impressão: folha no `<body>` no modo do kit, conteúdo, limpeza no `afterprint`, imprimir de novo e desmontar com a folha no ar.
2. `npm run dev`, abrir um paciente, aba **Anamnese**, salvar e clicar em **Imprimir** numa versão do histórico. No diálogo de impressão (ou em "Salvar como PDF") sai só a folha, em preto sobre branco, numa página A4, também com o tema escuro; a linha de assinatura fecha a página.
3. Cancelar o diálogo: a tela volta ao normal, e a folha não fica escondida na página (ela sai do `<body>`).

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[ImpressaoDaAnamnese]]
- [[HistoricoDeVersoes]]
- [[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]]
- [[2026]] (changelog)
