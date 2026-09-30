---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: /pacientes
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, lista, busca]
---

# Lista de pacientes

## O que é

A tela de entrada do módulo Pacientes: todos os pacientes da clínica em cartões, com busca por nome e por
telefone. É por ela que se acha quem chegou e se abre a [[FichaDoPaciente]].

## Onde está no código

- `src/modulos/pacientes/modulo.ts` — a rota `/pacientes` e o item `Pacientes` da coluna (grupo
  Consultório, ordem 20, ícone `users`, também na barra do celular).
- `src/modulos/pacientes/ListaPacientes.tsx` — a tela, com o selo de alertas da anamnese em cada cartão ([[SeloAlertas]]).
- `src/modulos/pacientes/busca.ts` — `filtrarPacientes`: a ordem e o filtro.
- `src/modulos/pacientes/exibicao.ts` — idade e convênio em texto (`anosDoPaciente`, `rotuloIdade`,
  `rotuloConvenio`); a idade vem de [[IdadeEFaixaEtaria]].
- Lê a coleção `pacientes` (`src/dados/colecoes.ts`) por `useColecao`.

## Comportamento

- **Ordem**: alfabética por nome (pt-BR); o acento não muda a posição.
- **Busca por nome**: por trecho, sem distinguir acento nem caixa — `joao` acha `João Pedro Alves`.
- **Busca por telefone**: por trecho dos dígitos, com ou sem máscara (`0002`, `90000-0002`,
  `(00) 90000-0002`). Só vale quando o termo inteiro é feito de dígitos e pontuação de máscara: `ana 9` é
  busca de nome. O `55` do país é aceito num número completo.
- **Contagem**: `8 pacientes`; com busca, `2 de 8 pacientes`. É uma região `role="status"`: o leitor de
  tela anuncia a cada letra digitada.
- **Cartão**: avatar com as iniciais, nome, `idade · convênio` e telefone. Paciente sem convênio aparece
  como `Particular`; nascimento ilegível omite a idade em vez de quebrar a lista. O cartão inteiro é um
  link para `/pacientes/:id`, a [[FichaDoPaciente]]. Quando a anamnese mais recente tem alertas, o cartão mostra o selo ([[SeloAlertas]]): uma pílula por alerta, como `Alergia informada: …`; sem alerta, não sobra espaço vazio.
- **Estados vazios**: sem nenhum paciente cadastrado, `Nenhum paciente cadastrado ainda`; com busca sem
  resultado, `Nenhum paciente encontrado` e o botão `Limpar busca`.
- **Sem paginação**: a lista inteira vai para a tela; a demo tem dezenas de pacientes.
- **Novo paciente**: o botão ao lado da busca leva ao [[CadastroDePaciente]] (`/pacientes/novo`).
- **Pendente**: os filtros por convênio e situação, item 1.8 do plano.

## Movimento e micro-interações

Cartões de vidro que sobem ao entrar na tela e levantam no hover (`GlassCard interactive`). O anel de foco
do link segue o raio de 26px do vidro. O kit já respeita `prefers-reduced-motion`.

## Histórico de mudanças

- [[2026-09-30-pr-051-lista-de-pacientes]] — a tela, a busca por nome e telefone e o item na coluna.
- [[2026-09-30-pr-071-cadastro-de-paciente]] — o botão `Novo paciente` ao lado da busca.
- [[2026-09-30-pr-076-ficha-do-paciente]] — o cartão passa a abrir a [[FichaDoPaciente]]; até então a rota `/pacientes/:id` não existia.
- [[2026-09-30-pr-119-alertas-do-paciente]] — o selo de alertas da anamnese no cartão.
