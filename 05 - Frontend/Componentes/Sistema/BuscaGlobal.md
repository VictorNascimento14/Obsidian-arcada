---
tipo: funcionalidade
camada: frontend
area: Sistema
rota: global (montada em /sistema)
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, sistema, busca, atalho]
---

# Busca global de pacientes

## O que é

Uma caixa de busca aberta por `Ctrl+K` (`⌘K` no Mac): digita-se o nome ou o telefone de um paciente e Enter abre a
ficha. É o caminho mais curto até um paciente, a partir de qualquer tela em que o componente esteja montado. Hoje ele
está montado na raiz das rotas (`src/rotas.tsx`) e responde em qualquer tela (seção «Montagem na casca»).

## Onde está no código

- `src/modulos/sistema/BuscaGlobal.tsx` — o componente: o atalho (registrado uma vez) e a `CaixaDeBusca` (`Modal`,
  campo e lista).
- `src/modulos/sistema/atalhoDeBusca.ts` — `abrirBusca()` e o evento que um botão usa para pedir a caixa.
- `src/modulos/sistema/CartaoDeBusca.tsx` — o cartão da tela [[Sistema]] com o atalho e o botão **Buscar**.
- `src/modulos/pacientes/busca.ts` — `filtrarPacientes`, reusado: nome sem acento nem caixa e telefone só pelos
  dígitos, em ordem alfabética (a mesma busca da [[ListaDePacientes]]).

## Comportamento

- **Abrir e fechar**: `Ctrl+K` ou `⌘K` (sem Shift nem Alt) abre; o mesmo atalho, o Escape, o X e o clique fora
  fecham. O atalho chama `preventDefault`: o navegador não fica com ele. Com outro diálogo aberto, o atalho não
  abre a busca por cima.
- **Registrado uma vez**: só a primeira instância montada responde ao atalho e ao `abrirBusca()`. Montada em dois
  lugares, abre uma caixa só.
- **Busca**: o foco já está no campo. Sem termo, a dica «Digite o nome ou o telefone do paciente.» e nenhuma lista.
  Com termo, no máximo 8 resultados (avatar, nome e telefone); acima disso, «Mostrando 8 de N. Continue digitando
  para refinar.»; sem resultado, «Nenhum paciente encontrado.».
- **Escolher**: ↓ e ↑ movem a seleção e param nas pontas (o mouse também a move). Enter abre a ficha do
  selecionado e o clique num resultado faz o mesmo: a caixa fecha e a rota vai para `/pacientes/:id`. Enter sem
  resultado não faz nada.
- **Acessibilidade**: o campo é um `combobox` (`aria-activedescendant` aponta a opção selecionada), a lista é um
  `listbox` de `option`, e um `role="status"` anuncia a dica, a contagem e a ausência de resultado.
- **Cada abertura começa do zero**: sem termo nem seleção. Nada é gravado.

## Montagem na casca

O registro de módulos (`import.meta.glob` dos `modulo.ts`) não tem ponto de encaixe global, e módulo não edita
arquivo compartilhado: por isso `<BuscaGlobal />` foi montado **uma vez, dentro do roteador**, no `element` da rota
raiz em `src/rotas.tsx` (PR #189). Fora do roteador não funciona: ao lado do `ToastHost` em
`App.tsx` o componente chamaria `useNavigate` sem roteador. Com a busca na casca, a instância da tela `/sistema`
segue inofensiva (só a primeira responde), e um botão de busca no cabeçalho pode chamar `abrirBusca()`.

## Movimento e micro-interações

O `Modal` do kit (véu escurecido, painel com `animate-rise`, fecha no Escape e no clique fora); o resultado selecionado fica
com fundo `primary-900/7%`. O foco vai para o campo por um efeito próprio: ver
[[2026-09-30-modal-do-kit-tira-o-foco-do-campo]].

## Histórico de mudanças

- [[2026-09-30-pr-186-sistema-busca]] — cria a busca global e o cartão na tela Sistema.
