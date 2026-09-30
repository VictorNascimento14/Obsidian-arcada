---
tipo: funcionalidade
camada: frontend
area: Sistema
rota: /sistema
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, sistema, dados]
---

# Restauração da demonstração (cartão)

## O que é

O segundo cartão da tela [[Sistema]]: um botão que apaga tudo o que o navegador guarda do Arcada e recarrega a
página, para as sementes plantarem de novo os dados fictícios de exemplo. Serve para recomeçar a demonstração
sem abrir as ferramentas do navegador.

## Onde está no código

- `src/modulos/sistema/CartaoDeRestauracao.tsx` — o cartão e o modal de confirmação.
- `src/dados/backup.ts` — `restaurarDemonstracao()`: apaga as chaves `arcada:*`, marcas de semente incluídas; o
  resto do `localStorage` fica. A limpeza é a mesma troca de chaves do importador do [[Sistema]].
- `src/dados/sementes.ts` — `carregarSementes`: quem planta a demonstração no carregamento seguinte. Por que a
  marca de semente também é apagada: [[2026-09-30-restaurar-a-demonstracao-apaga-tambem-a-marca-de-semente]].

## Comportamento

- **Restaurar demonstração** abre o modal «Apagar tudo e restaurar a demonstração?», que diz o que será apagado
  (pacientes, agenda, financeiro e prontuário), que não dá para desfazer e que convém exportar um backup antes.
- **Cancelar** (ou Esc, ou clique fora) fecha sem mudar nada.
- **Apagar e restaurar** apaga as chaves `arcada:*` e recarrega a página; no carregamento seguinte, as sementes
  plantam a demonstração de novo.
- **O que não é apagado**: o tema e a coluna lateral recolhida (`arcada-tema`, `arcada-sidebar-collapsed`, com
  traço, fora do prefixo) e as chaves de outro app.

## Movimento e micro-interações

Sem movimento próprio: o cartão é o `GlassCard` do kit e a confirmação é o `Modal` (véu escurecido, fecha no
Escape e no clique fora).

## Histórico de mudanças

- [[2026-09-30-pr-178-sistema-restaurar]] — cria o cartão e `restaurarDemonstracao`.
