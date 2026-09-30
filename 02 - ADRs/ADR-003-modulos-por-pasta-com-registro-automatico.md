---
tipo: adr
numero: 3
data: 2026-09-30
status: aceito
tags: [adr, arquitetura, frontend, modulos]
---

# ADR-003 — Módulos por pasta, com registro automático

## Contexto

O Arcada tem quatorze módulos além da fundação — pacientes, anamnese, odontograma, periodontograma,
agenda, financeiro… — e o plano é entregá-los em PRs pequenos, com **trilhas em paralelo**, uma por módulo
([[2026-09-30-plano-da-v1]]).

Se cada módulo precisar escrever numa lista central — a tabela de rotas, os itens da coluna lateral, as
abas da ficha do paciente —, todo PR de módulo edita o mesmo arquivo, e o conflito de merge é certo.

## Decisão

1. Cada módulo mora em `src/modulos/<modulo>/` — telas, componentes, regras e testes — e exporta `modulo`
   do seu `modulo.tsx`: as **rotas**, o **item da coluna lateral** e, se tiver, a **aba da ficha do
   paciente**.
2. O registro (`src/modulos/index.ts`) acha todos por **`import.meta.glob`**. Módulo novo **não edita
   arquivo compartilhado**: cria a pasta e exporta `modulo`.
3. O que mais de um módulo usa fica fora dos módulos: regras puras em `src/dominio/`, peças em
   `src/componentes/` e dados em `src/dados/`.
4. A **ordem** do item da coluna e da aba sai de um campo **`ordem`** do `modulo`, e o registro ordena por
   ele.

## Consequências

**A favor**

- Dois PRs de módulos diferentes não tocam o mesmo arquivo: andam em paralelo sem conflito. Tirar um
  módulo é apagar a pasta.
- Coluna, rotas e abas nascem do que o registro achou: não existe lista para esquecer de atualizar.

**Custos**

- **A ordem deixa de ser implícita.** O `import.meta.glob` não dá ordem de produto — ela seguiria os
  nomes das pastas —, então cada módulo declara `ordem`. É o custo da decisão. Dois módulos podem
  declarar o mesmo valor; como o registro desempata é `TODO: confirmar no PR do registro de módulos`.
- **Falha silenciosa.** Pasta sem `modulo.tsx`, ou cujo arquivo não exporta `modulo`, simplesmente não
  aparece. Um teste do registro que compare as pastas com os módulos achados fecha esse buraco
  (`TODO: confirmar no PR do registro de módulos`).
- **O caminho vira contrato.** O nome e o lugar do arquivo (`src/modulos/<modulo>/modulo.tsx`) passam a
  fazer parte da convenção do projeto.

Relacionadas: [[ADR-002-sistema-visual-vidro-organico]] (a coluna lateral é montada uma vez pela casca do
kit, e cada módulo contribui só com o seu item) e [[ADR-001-frontend-primeiro-com-dados-locais]] (dados
sempre por `src/dados/`, nunca dentro do módulo).

## Implementado em

TODO: PR que implementar (previsto em [[2026-09-30-plano-da-v1]]).
