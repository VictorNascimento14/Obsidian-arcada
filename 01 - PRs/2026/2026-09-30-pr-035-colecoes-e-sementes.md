---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 35
url: https://github.com/VictorNascimento14/Arcada/pull/35
branch: feat/colecoes-e-sementes
tags: [pr, fundacao, dados]
status: aberto
---

# PR #35 — feat(dados): coleções do núcleo e dados de demonstração fictícios

## 🎯 Contexto

Módulo 0 do [[2026-09-30-plano-da-v1]]. Segue [[ADR-001-frontend-primeiro-com-dados-locais]] e [[ADR-003-modulos-por-pasta-com-registro-automatico]]. Fecha a issue #11.

## 🔧 Mudanças

- `src/dados/colecoes.ts` — as oito coleções do núcleo; a clínica é um registro só (`CLINICA_ID`).
- `src/dados/sementes.ts` (+ teste) — `Semeador`, `semeadorDoNucleo`, `carregarSementes` com marca `arcada:sementes:<chave>` = versão.
- `src/dados/semeadores.ts` — o núcleo e os semeadores dos módulos por `import.meta.glob`.
- `src/main.tsx` — `carregarSementes` antes do `createRoot`.

## 🕵️ Dado pessoal (LGPD)

Primeiros dados de pessoa no app — todos fictícios: nomes genéricos, **nenhum CPF**, telefones com DDD 00 (que não existe), e-mail só o estável `paciente@exemplo.com`. Ficam no `localStorage` do navegador de quem abre a demo.

## 🧠 Decisões técnicas

- Semeador roda uma vez por versão e só preenche coleção **vazia**: recarregar a página nunca apaga o que o usuário mudou; subir a versão replanta só o que estiver vazio.
- Sementes por módulo, achadas por `glob`, como as rotas: o catálogo de procedimentos e as consultas de exemplo entram com os módulos deles, sem editar arquivo do núcleo.
- Sem storage (bloqueado), semeia em memória a cada carga: a demo continua de pé.

## 🧪 Como testar

1. Aba anônima → `localhost:3000` abre com os dados de demonstração (visíveis quando a lista de pacientes existir).
2. `npm test` → 4 testes: planta na primeira carga, roda uma vez por versão, não sobrescreve dado do usuário, nenhum CPF.

## 📎 Documentação afetada

- [[ADR-001-frontend-primeiro-com-dados-locais]]
- [[2026]] (changelog)
