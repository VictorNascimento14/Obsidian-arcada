---
tipo: funcionalidade
camada: frontend
area: Painel
rota: /
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, painel, tela-inicial]
---

# Painel do consultório

## O que é

A tela inicial do Arcada, na rota `/`: o consultório num relance. É o primeiro item da coluna lateral (grupo
Consultório, ícone de grade) e da barra de baixo do celular. A tela é montada por blocos, e cada bloco tem a sua nota
em `05 - Frontend/Componentes/Painel`; hoje há o [[ConsultasDeHoje]], depois do cartão de boas-vindas. Os indicadores
do mês, os tratamentos em aberto e o faturamento por semana entram pelos itens 13.2, 13.3 e 13.5 do
[[2026-09-30-plano-da-v1]].

## Onde está no código

- `src/modulos/painel/modulo.ts` — a rota `/` e o item da coluna (grupo `consultorio`, ordem 0, ativo só no caminho
  exato, na barra do celular).
- `src/modulos/painel/Painel.tsx` — a tela: `PageShell` com a data por extenso no cabeçalho, o cartão de boas-vindas e
  os blocos. Guarda o minuto de agora (`useAgora`) e o repassa aos blocos.

## Comportamento

- **Só leitura.** O painel não grava nada: lê as coleções do núcleo pelos hooks de `src/dados/`.
- **O relógio.** `useAgora` guarda o minuto de agora (`AAAA-MM-DDTHH:mm`, horário local) e o renova a cada 60 s,
  contados da montagem. O painel fica aberto o dia inteiro; sem o relógio, a virada do dia e a próxima consulta só
  mudariam ao recarregar. O horário pode atrasar até 59 s. Os blocos recebem o "agora" por propriedade, em vez de cada
  um ter o seu temporizador.
- **Boas-vindas.** O cartão `Bem-vinda ao Arcada` continua no topo: o teste do `App` o usa para reconhecer a tela
  inicial.

## Movimento e micro-interações

Cartões de vidro (`GlassCard`) que sobem ao entrar na tela.

## Histórico de mudanças

- [[2026-09-30-pr-170-painel-hoje]] — o bloco Consultas de hoje e o relógio da tela.
