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
empilhados. Hoje há um, o **backup dos dados**. A restauração da demonstração e a busca global entram depois,
no mesmo módulo.

## Onde está no código

- `src/modulos/sistema/modulo.ts` — registra a rota `/sistema` e o item da coluna (grupo `cadastros`, ordem 90,
  ícone `shield-check`, rótulo «Sistema»); o registro acha o módulo sozinho
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- `src/modulos/sistema/PaginaSistema.tsx` — a página: `PageShell` e os cartões.
- `src/modulos/sistema/CartaoDeBackup.tsx` — o cartão de exportar e importar.
- `src/dados/backup.ts` — a regra: `exportarBackup`, `lerBackup` e `substituirPor`. Mora em `src/dados/` porque
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

## Movimento e micro-interações

Sem movimento próprio: o cartão é o `GlassCard` do kit e o modal de confirmação é o `Modal` (véu escurecido,
fecha no Escape e no clique fora). Exportar confirma com um aviso passageiro («Backup exportado»).

## Histórico de mudanças

- [[2026-09-30-pr-171-sistema-backup]] — Backup: exportar e importar, e a rota `/sistema` com o item da coluna.
