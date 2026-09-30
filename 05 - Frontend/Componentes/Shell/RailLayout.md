---
tipo: funcionalidade
camada: frontend
area: Shell
rota: todas as telas com coluna
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, shell]
---

# RailLayout

## O que é

A rota de layout que monta a coluna lateral **uma vez** e entrega às telas, pelo contexto do
roteador, a navegação, a conta e a função de abrir o menu do celular. Vem do kit vidro-orgânico.

## Onde está no código

- `src/ui/shell/RailLayout.tsx` (kit) — layout e contexto.
- `src/rotas.tsx` — a árvore de rotas: o `RailLayout` com os grupos, a barra do celular e as rotas que o registro
  de módulos achou (`src/modulos/index.ts`, [[ADR-003-modulos-por-pasta-com-registro-automatico]]); as telas de
  sistema ficam fora dele.
- `src/App.tsx` — cria o roteador, com `basename: import.meta.env.BASE_URL`.
- `src/navegacao.tsx` — só a conta de demonstração; os grupos e itens da coluna vêm dos módulos.

## Comportamento

- Toda tela com coluna é filha desta rota e passa pelo `PageShell` (cabeçalho que gruda, barra de
  baixo no celular). Tela com coluna não tem rodapé.
- Clicar num item só desliza o marcador; a coluna não recolhe nem pisca.
- A barra de baixo do celular mostra os itens da coluna na ordem (ou os escolhidos por `barraCelular`).

## Movimento e micro-interações

Coluna 76↔236px com `--dur-open`/`--dur-close` e curva `--ease-organic`; espiada por hover com a
coluna recolhida. Detalhes nas invariantes do `CLAUDE.md` do repositório de código.

## Histórico de mudanças

- [[2026-09-30-pr-018-casca]] — casca instalada com o grupo "Consultório" e a tela Painel provisória.
- [[2026-09-30-pr-022-registro-de-modulos]] — os grupos e itens da coluna passam a vir do registro de módulos; `src/navegacao.tsx` fica só com a conta.
- [[2026-09-30-pr-023-telas-de-sistema]] — a árvore de rotas vai para `src/rotas.tsx`, com a página não encontrada e o erro inesperado fora da casca.
