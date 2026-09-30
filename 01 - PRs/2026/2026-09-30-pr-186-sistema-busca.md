---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 186
url: https://github.com/VictorNascimento14/Arcada/pull/186
branch: feat/sistema-busca
tags: [pr, sistema, busca, atalho, acessibilidade]
status: merged
---

# PR #186 — feat(sistema): buscar pacientes pelo atalho Ctrl+K e abrir a ficha com Enter

## 🎯 Contexto

Item 14.2 (Busca global) do [[2026-09-30-plano-da-v1]], terceiro PR do módulo **Sistema** desta trilha, sobre a tela `/sistema` ([[Sistema]]). Reusa `filtrarPacientes` (nome sem acento e telefone, da busca da [[ListaDePacientes]]) e o `Modal` do kit. Fecha a issue #183.

## 🔧 Mudanças

- `src/modulos/sistema/BuscaGlobal.tsx` (+ teste): o atalho `Ctrl+K`/`⌘K`, a caixa (`Modal`, campo e lista) e a navegação para a ficha.
- `src/modulos/sistema/atalhoDeBusca.ts`: `abrirBusca()`, o evento que o botão usa para pedir a caixa à instância montada.
- `src/modulos/sistema/CartaoDeBusca.tsx`: o cartão com o atalho e o botão **Buscar**.
- `src/modulos/sistema/PaginaSistema.tsx` (+ teste): monta o `BuscaGlobal` e o cartão.

## 🕵️ Dado pessoal (LGPD)

A caixa mostra nome e telefone do paciente, os mesmos dados da lista de pacientes. Nada é gravado nem enviado: o termo vive só no estado da caixa e some ao fechar. Sem CPF. Testes e semente só usam dado fictício.

## 🧠 Decisões técnicas

- **Montar na casca fica para o orquestrador.** O registro de módulos (`import.meta.glob` dos `modulo.ts`) não tem ponto de encaixe global e módulo não edita arquivo compartilhado; por isso a busca é um componente exportado. **Falta montá-lo uma vez, DENTRO do roteador** (por exemplo, no `element` da rota raiz de `src/rotas.tsx`, ao lado do `RailLayout`): ele usa `useNavigate`, então ao lado do `ToastHost` em `App.tsx`, que fica fora do roteador, não funciona. Enquanto isso, o atalho só vale na tela `/sistema`, que o monta.
- **«Registrado uma vez» é garantia do componente.** Uma lista de instâncias montadas faz só a primeira responder ao `Ctrl+K` e ao evento de `abrirBusca`: quando a casca montar a busca, a da tela `/sistema` fica inerte, sem abrir duas caixas nem abrir e fechar a mesma.
- **Não abre por cima de outro diálogo.** O Escape do `Modal` do kit fecha todos os abertos de uma vez; uma busca aberta sobre o formulário de uma consulta o perderia junto.
- **Só Ctrl ou ⌘ com K, sem Shift nem Alt** (`Ctrl+Shift+K` é o console do Firefox). O atalho chama `preventDefault`, para o navegador não ir para a barra de endereço.
- **O botão pede por evento, não por estado.** `abrirBusca()` dispara um evento na janela e a instância dona abre a caixa: o cartão não enxerga o componente, e o botão que a casca ganhar depois usa a mesma função.
- **Reuso**: `filtrarPacientes` (nome sem acento, telefone só pelos dígitos, ordem alfabética), `Modal`, `TextField`, `Avatar` e `useColecao`.
- **Sem termo, sem lista**: a caixa mostra a dica em vez dos pacientes (a lista pode ter centenas); com termo, no máximo 8 resultados e o aviso «Mostrando 8 de N».
- **Padrão combobox/listbox**: o campo é `combobox` com `aria-activedescendant`, a lista é `listbox` de `option`, e o foco fica no campo (as setas só movem a seleção). Um `role="status"` anuncia a dica, a contagem e o «Nenhum paciente encontrado».
- **A caixa só existe montada enquanto aberta**: cada abertura começa do zero, sem termo nem seleção.

## ⚠️ Armadilhas e aprendizados

- O `Modal` do kit dá o foco ao botão Fechar num `useEffect`: `autoFocus` no campo de dentro dele perde o foco (conferido, o teste do foco falha). O foco vai por um efeito do componente que monta o `Modal` ([[2026-09-30-modal-do-kit-tira-o-foco-do-campo]]).
- `onMouseMove`, e não `onMouseEnter`, move a seleção com o mouse: com as setas a lista rola sob um mouse parado, e `mouseenter` faria a seleção pular sozinha.
- `e.key === "k" || e.key === "K"` em vez de `e.key.toLowerCase()`: o autopreenchimento do Chrome dispara `keydown` sem `key`, e chamar `toLowerCase` em `undefined` lançaria dentro do ouvinte do documento.
- Em teste, `abrirBusca()` chamado direto atualiza estado fora do React e pede `act()`; o atalho se dispara com `fireEvent.keyDown(document, …)`.
- Os testes do foco, da instância única e da guarda de diálogo foram conferidos por mutação: sem o efeito no pai, sem a lista de instâncias e sem a guarda, cada um falha.

## 🧪 Como testar

1. Abra `/sistema` e clique em **Buscar** (ou pressione `Ctrl+K`, `⌘K` no Mac): abre «Buscar paciente» com o foco no campo e a dica «Digite o nome ou o telefone do paciente».
2. Digite `joao`: aparece «João Pedro Alves», sem precisar do acento. Digite `zzz`: «Nenhum paciente encontrado».
3. Com resultados, use ↓ e ↑ para mover a seleção e Enter: abre a ficha do paciente e fecha a caixa. Clicar num resultado faz o mesmo.
4. Abra de novo, digite algo e pressione `Escape` (ou `Ctrl+K`): fecha sem navegar, e a abertura seguinte começa com o campo vazio.

## 📎 Documentação afetada

- [[BuscaGlobal]]
- [[Sistema]]
- [[2026-09-30-modal-do-kit-tira-o-foco-do-campo]]
- [[ListaDePacientes]]
- [[2026]] (changelog)
