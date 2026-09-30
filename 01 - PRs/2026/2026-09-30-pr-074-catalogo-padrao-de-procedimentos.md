---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 74
url: https://github.com/VictorNascimento14/Arcada/pull/74
branch: feat/procedimentos-catalogo
tags: [pr, procedimentos, catalogo]
status: merged
---

# PR #74 — feat(procedimentos): plantar o catálogo padrão de procedimentos por especialidade

## 🎯 Contexto

Item 6.1 (Catálogo padrão por especialidade) do [[2026-09-30-plano-da-v1]], o primeiro do módulo Procedimentos. A coleção `procedimentos`, o tipo `Procedimento` e o mecanismo de sementes por módulo (`src/modulos/<modulo>/sementes.ts`) vieram do PR #35; as condições do odontograma que o catálogo cita, do PR #70. Fecha a issue #73.

## 🔧 Mudanças

- `src/modulos/procedimentos/catalogo.ts` (+ teste): `CATALOGO`, os 32 procedimentos, e `ESPECIALIDADES`, as oito áreas na ordem da lista.
- `src/modulos/procedimentos/sementes.ts` (+ teste): o `semeador`, que planta o catálogo na coleção `procedimentos` quando ela está vazia.
- `src/dominio/procedimento.ts`: `Procedimento` ganha `codigo` e `condicaoResultante`, os dois opcionais.

## 🧠 Decisões técnicas

- **`codigo` e `condicaoResultante` são opcionais.** O que já está gravado no navegador e o que outros módulos montam em teste não os têm, e um campo obrigatório quebraria todos. O item 6.3 decide se o formulário pede o código.
- **`condicaoResultante` é `string`, não `CondicaoId`.** `src/dominio/` não importa módulo. Quem garante que o id existe é o teste do catálogo, que confere cada um contra `CONDICOES` (`src/modulos/odontograma/condicoes.ts`).
- **Código próprio, no formato `PRE-02`**: sigla de três letras da especialidade e dois dígitos. Não usa a numeração da TUSS (oito dígitos, dos convênios): o catálogo é demonstração e o código é da clínica.
- **Ids legíveis (`proc-profilaxia`)**, como na semente do núcleo (`pac-ana`): outra semente ou teste cita um procedimento sem consultar o catálogo.
- **Só tem condição o ato que muda a marca do dente**: restauração e selante (de face, por isso exigem dente e face), exodontia (`ausente`), canal e retratamento (`tratamentoDeCanal`), coroa e implante. Enxerto, núcleo e coroa sobre implante ficam sem: o dente já consta como implante.
- **A semente planta só em coleção vazia e roda uma vez por `versao`.** Subir a versão faz o semeador rodar de novo, mas ele continua sem tocar numa coleção que já tem procedimento: a tabela editada pela clínica nunca é reposta.
- **Catálogo é dado, não regra**: uma lista de objetos completos, sem fábrica que esconda campo. Preços e durações são fictícios.

## ⚠️ Armadilhas e aprendizados

- O id da condição de canal é **`tratamentoDeCanal`** (camelCase), não `tratamento-canal`. O teste do catálogo falha com um id que `CONDICOES` não tem.
- **Selante é condição de face** no odontograma (escopo `face`): o procedimento `PRE-04` exige a face. O teste compara `exigeFace` com o escopo da condição, para o desenho do dente saber onde marcar.
- `SEMEADORES` acha o arquivo por `import.meta.glob` em `../modulos/*/sementes.ts` e usa a exportação **`semeador`**: com outro nome, `m.semeador` seria `undefined` e `carregarSementes` quebraria ao abrir o app. O teste `é achada pelo registro de semeadores` cobre isso.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/procedimentos` — o catálogo (contagem, unicidade, preço e duração inteiros, dente e face, condições do odontograma) e a semente (planta na primeira carga, não sobrescreve, é achada pelo registro).
2. Com o app rodando (`npm run dev`) e sem a chave `arcada:procedimentos` no `localStorage` (Application, no DevTools), recarregue a página: a chave passa a guardar os 32 procedimentos.
3. Troque a lista dessa chave por um único procedimento, apague `arcada:sementes:procedimentos` e recarregue: o catálogo não volta, porque a coleção não está vazia.

## 📎 Documentação afetada

- [[CatalogoPadrao]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
