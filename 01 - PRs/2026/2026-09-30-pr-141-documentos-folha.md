---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 141
url: https://github.com/VictorNascimento14/Arcada/pull/141
branch: feat/documentos-folha
tags: [pr, documentos, impressao, assinatura]
status: merged
---

# PR #141 — feat(documentos): folha impressa com cabeçalho da clínica e linha de assinatura

## 🎯 Contexto

Item 12.1 (Modelo de impressão com cabeçalho da clínica) do [[2026-09-30-plano-da-v1]]. Extrai para `src/componentes/` o mecanismo de [[ImpressaoDaAnamnese]] (PR #131), como o aprendizado [[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]] previa para o segundo módulo que imprimisse. Lê o cadastro da clínica de [[Clinica]]. Fecha a issue #134.

## 🔧 Mudanças

- `src/componentes/FolhaImpressa.tsx`: a folha, por portal direto no `<body>` com `data-print-clone`; lê a clínica (`CLINICA_ID`) por `useColecao` e o profissional vem por prop.
- `src/componentes/useImpressao.ts`: `useImpressao<T>()` devolve `{ folha, imprimir }`; `imprimir(dados)` guarda os dados, liga `data-print-mode="clone"` e chama `window.print()`; o `afterprint` limpa.
- `src/componentes/FolhaImpressa.test.tsx` e `useImpressao.test.tsx`: 8 testes.

## 🧠 Decisões técnicas

- **O hook guarda os dados da impressão, não só um sinal.** `imprimir(dados)` põe os dados em `folha` até o `afterprint`: a tela desenha a folha a partir de um retrato do que foi pedido, e o que se imprime não muda se o formulário mudar por baixo. É o que também deixa a anamnese migrar (`useImpressao<{ versao, numero }>()`).
- **Um objeto novo por chamada.** Guardar `{ dados }` refaz o efeito a cada clique, inclusive para os mesmos dados sem `afterprint` no meio, como no efeito original da anamnese.
- **O cabeçalho vem do cadastro da clínica, dentro da folha.** Quem usa a peça não repassa nome, telefone e endereço: a folha lê o registro `CLINICA_ID` e acompanha o cadastro. Endereço e cidade/UF ficam numa linha (`Rua Exemplo, 100 — São Paulo/SP`), sem separador sobrando quando falta um campo; sem clínica cadastrada, a folha sai sem cabeçalho, mas com o resto.
- **Rodapé com o profissional, obrigatório.** A linha de assinatura (`<hr>`) fica acima do nome e do CRO, com `mt-12` de espaço para assinar e `break-inside-avoid break-before-avoid` para não ficar sozinha numa página. O rodapé é do profissional: a anamnese assina o paciente e, para migrar, a peça precisaria de uma legenda de assinatura configurável, o que ninguém pede ainda.
- **Cor fixa em preto sobre branco** (`text-black`, `bg-white`), como na folha da anamnese: no tema escuro os tokens do app são claros e sumiriam no papel.
- **Sem tocar a anamnese nem o kit.** `FolhaDaAnamnese` e `HistoricoDeVersoes` continuam com o mecanismo próprio; o CSS de impressão vem de `src/ui/index.css`, que não se edita.

## ⚠️ Armadilhas e aprendizados

- A folha só existe no DOM durante a impressão: quem usa a peça renderiza `{folha && <FolhaImpressa …>}`. Deixá-la sempre montada esconderia, na página, uma segunda cópia do documento (e do nome do paciente).
- O `afterprint` também vem do `printToPDF` do Chrome: quem conferir por protocolo de depuração precisa acionar a impressão de novo antes de cada PDF (ver [[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]]).

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/componentes` — a folha (cabeçalho da clínica, título, corpo, linha de assinatura com nome e CRO, clínica só com o nome, clínica trocada e removida) e o mecanismo (folha no `<body>` no modo do kit antes do `window.print`, limpeza no `afterprint`, imprimir de novo, desmontar com a folha no ar).
2. Nenhuma tela usa a folha neste PR, então não há o que abrir no navegador: a conferência do papel (PDF, temas claro e escuro) vem com o receituário, primeira tela que a usa.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[FolhaImpressa]]
- [[ImpressaoDaAnamnese]]
- [[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]]
- [[2026]] (changelog)
