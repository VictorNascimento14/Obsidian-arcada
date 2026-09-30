---
tipo: funcionalidade
camada: frontend
area: Sistema
rota: /sistema
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, sistema, backup]
---

# Sistema (tela)

## O que é

A tela de sistema, no grupo **Cadastros** da coluna lateral: um cartão por assunto dos dados do navegador,
empilhados. Hoje há três: o **backup dos dados**, a **restauração da demonstração** ([[RestauracaoDaDemonstracao]]) e a **busca de pacientes** ([[BuscaGlobal]]), cujo atalho `Ctrl+K` está montado na raiz das rotas e vale em qualquer tela.

## Onde está no código

- `src/modulos/sistema/modulo.ts` — registra a rota `/sistema` e o item da coluna (grupo `cadastros`, ordem 90,
  ícone `shield-check`, rótulo «Sistema»); o registro acha o módulo sozinho
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- `src/modulos/sistema/PaginaSistema.tsx` — a página: `PageShell` e os cartões.
- `src/modulos/sistema/CartaoDeBackup.tsx` — o cartão de exportar e importar.
- `src/modulos/sistema/CartaoDeRestauracao.tsx` — o cartão que apaga o que o navegador guarda e recarrega a
  demonstração ([[RestauracaoDaDemonstracao]]).
- `src/modulos/sistema/CartaoDeBusca.tsx`, `BuscaGlobal.tsx` e `atalhoDeBusca.ts` — o cartão do atalho, a caixa
  de busca e o evento que a abre ([[BuscaGlobal]]).
- `src/rotas.tsx` — a raiz das rotas monta a `BuscaGlobal` (PR #189): o `Ctrl+K` abre a busca em qualquer tela,
  e só uma instância responde.
- `src/dados/backup.ts` — a regra: `exportarBackup`, `lerBackup`, `substituirPor` e `restaurarDemonstracao`. Mora em `src/dados/` porque
  só lá se toca o `localStorage` ([[ADR-001-frontend-primeiro-com-dados-locais]]).

## Comportamento

### Backup dos dados

- **Exportar backup** baixa `arcada-backup-AAAA-MM-DD.json` (o dia é o local). O arquivo é
  `{ app: "arcada", versao, exportadoEm, dados }`, e `dados` tem o texto de cada chave `arcada:*` do
  `localStorage`: coleções e marcas de semente. Ficam de fora as preferências do kit (`arcada-tema`,
  `arcada-sidebar-collapsed`, que usam traço) e as chaves de outro app. Sem nenhum dado salvo, a tela avisa e
  não baixa arquivo vazio.
- **Importar backup** abre o seletor de arquivo (que sugere `.json`). O arquivo é conferido **antes** de
  qualquer mudança: precisa ser JSON, ter `app: "arcada"`, uma versão inteira que o app conheça (uma mais nova
  pede para atualizar o app), data de exportação válida e só chaves `arcada:*`, cada coleção no formato
  `{ versao, itens }` com `id` em cada item. Se falhar, o motivo aparece em vermelho e nada é alterado.
- **Confirmação**: arquivo válido abre o modal «Substituir os dados atuais?» com a data e a hora do backup e o
  aviso de que não dá para desfazer (e de que convém exportar antes). **Cancelar** fecha sem mudar nada.
  **Substituir os dados** apaga as chaves `arcada:*`, grava as do arquivo e recarrega a página, porque as
  coleções guardam o estado em memória.
- **Se a gravação falhar** (armazenamento cheio ou bloqueado), os dados de antes voltam, a tela mostra o erro e
  a página não recarrega.
- **Aviso de dado de saúde** fixo no cartão: o arquivo tem dado pessoal sensível; guardar em lugar seguro e não
  enviar por canal aberto.

### Restaurar a demonstração

O segundo cartão apaga tudo o que o navegador guarda do Arcada e recarrega a página, para as sementes plantarem
os dados fictícios de novo. Pede confirmação, avisa que não dá para desfazer e lembra de exportar um backup
antes. O detalhe está em [[RestauracaoDaDemonstracao]].

### Busca de pacientes

O terceiro cartão mostra o atalho `Ctrl+K` (`⌘K` no Mac) e o botão **Buscar**, que abre a caixa de busca por
nome ou telefone; Enter abre a ficha do paciente. A caixa está montada na raiz das rotas, então o atalho abre a
mesma caixa em qualquer tela, não só nesta. O detalhe está em [[BuscaGlobal]].

## Movimento e micro-interações

Sem movimento próprio: o cartão é o `GlassCard` do kit e o modal de confirmação é o `Modal` (véu escurecido,
fecha no Escape e no clique fora). Exportar confirma com um aviso passageiro («Backup exportado»).

## Histórico de mudanças

- [[2026-09-30-pr-171-sistema-backup]] — Backup: exportar e importar, e a rota `/sistema` com o item da coluna.
- [[2026-09-30-pr-178-sistema-restaurar]] — o cartão de restaurar a demonstração.
- [[2026-09-30-pr-186-sistema-busca]] — o cartão da busca de pacientes, com o atalho `Ctrl+K`.
- [[2026-09-30-pr-189-busca-na-casca]] — a busca montada na raiz das rotas: `Ctrl+K` em qualquer tela.
