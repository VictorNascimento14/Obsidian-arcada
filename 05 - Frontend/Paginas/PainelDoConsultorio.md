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
Consultório, ícone de grade) e da barra de baixo do celular. A tela é montada por blocos, e cada bloco tem a sua nota em `05 - Frontend/Componentes/Painel`. Depois do cartão
de boas-vindas vêm quatro, nesta ordem: os [[IndicadoresDoMes]] (o faturamento recebido, as consultas e a taxa
de faltas do mês), os [[TratamentosEmAberto]] (a contagem e o valor dos orçamentos e dos tratamentos em aberto),
as [[ConsultasDeHoje]] (o dia da agenda, com a próxima em destaque) e o [[FaturamentoPorSemana]] (o recebido nas
últimas seis semanas, em barras). A partir de `xl`, as consultas de hoje e o faturamento ficam lado a lado, em
duas colunas; abaixo disso, empilhados. São os itens 13.1, 13.2, 13.3 e 13.5 do [[2026-09-30-plano-da-v1]].

## Onde está no código

- `src/modulos/painel/modulo.ts` — a rota `/` e o item da coluna (grupo `consultorio`, ordem 0, ativo só no caminho
  exato, na barra do celular).
- `src/modulos/painel/Painel.tsx` — a tela: `PageShell` com a data por extenso no cabeçalho, o cartão de boas-vindas e
  os blocos. Guarda o minuto de agora (`useAgora`) e o repassa aos blocos.
- Os blocos, um arquivo cada: `IndicadoresDoMes.tsx`, `TratamentosEmAberto.tsx`, `ConsultasDeHoje.tsx` e
  `FaturamentoPorSemana.tsx`. Os dois últimos ficam numa grade de duas colunas a partir do `xl`
  (`xl:grid-cols-[minmax(0,3fr)_minmax(0,2fr)]`: as consultas com a parte maior).

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
- [[2026-09-30-pr-177-painel-indicadores]] — o bloco Indicadores do mês.
- [[2026-09-30-pr-182-painel-em-aberto]] — o bloco Planos em aberto.
- [[2026-09-30-pr-190-painel-faturamento]] — o bloco Faturamento por semana, ao lado das consultas de hoje a partir do `xl`.
