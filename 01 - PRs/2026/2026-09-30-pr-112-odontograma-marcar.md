---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 112
url: https://github.com/VictorNascimento14/Arcada/pull/112
branch: feat/odontograma-marcar
tags: [pr, odontograma, condicoes, acessibilidade, ficha]
status: merged
---

# PR #112 — feat(odontograma): marcar condição por face e por dente na ficha do paciente

## 🎯 Contexto

Item 3.7 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): marcar condição por face e por dente sobre o `Dente` (3.4), as arcadas (3.5) e a dentição (3.6), com as condições de 3.3. A aba entra na ficha do paciente pelo registro de módulos (ADR-003), sem editar a ficha. Fecha a issue #100.

## 🔧 Mudanças

- `src/modulos/odontograma/marcas.ts` — `Marca`, `alternarMarca` (liga e desliga) e `conferirMarca` (valida dente, face e condição), puros.
- `src/modulos/odontograma/dados.ts` — a coleção `odontogramas`, o hook `useMarcas` e `alternarMarcaDoPaciente` (valida e grava).
- `src/modulos/odontograma/desenho.tsx` — a forma das faces, a marca de face, o símbolo de dente inteiro e o `IconeDaCondicao`.
- `src/modulos/odontograma/BarraDeCondicoes.tsx`, `AbaOdontograma.tsx` e `modulo.ts` — a barra, a aba e o registro da aba na ficha (ordem 20).
- `src/modulos/odontograma/Dente.tsx` e `Odontograma.tsx` — recebem as marcas e as ações; o número do dente vira botão quando a condição escolhida é de dente inteiro.
- `src/modulos/odontograma/Legenda.tsx` e `condicoes.ts` — a legenda passa a mostrar o símbolo de cada condição; `condicoes.ts` ganha `CONDICAO_POR_ID`, os tipos de face e de dente, `ehCondicaoDeFace` e `GRUPOS_DE_ESCOPO`.
- Testes ao lado de cada arquivo, e `AbaOdontograma.test.tsx` com o fluxo inteiro.
- [[MarcarCondicoes]] — nota nova de componente no cofre.

## 🕵️ Dado pessoal (LGPD)

As marcas são dado de saúde do paciente. Na v1 ficam só no `localStorage` do navegador desta máquina, como o resto do app (ADR-001), sem sair dela. Nada apaga o odontograma quando o paciente é excluído — o módulo de pacientes ainda não exclui: quem fizer a exclusão precisa remover a entrada de `odontogramas`. Nenhum dado real de pessoa em código, teste ou captura: só `Paciente Exemplo` e nomes fictícios da semente.

## 🧠 Decisões técnicas

- **Uma face tem no máximo uma condição; o dente inteiro pode ter várias.** Cárie, restauração e selante são o estado da superfície, então marcar outra substitui. Tratamento de canal e coroa, ao contrário, convivem no mesmo dente com frequência, e cada uma liga e desliga por si.
- **Lista plana de marcas `{ dente, face?, condicao }`, não mapa por dente.** Casa com uma tabela de um backend e deixa simples o que vem depois: limpar o dente (3.8), fixar o estado inicial (3.9) e contar por condição (3.10). O `id` da entrada é o do paciente.
- **A regra é pura (`marcas.ts`) e a validação está na escrita (`conferirMarca`, chamada por `alternarMarcaDoPaciente`).** A validação da tela é conforto; a de `src/dados/` é a regra. Ela usa `CONDICOES.find`, e não um objeto indexado pelo texto recebido: `"constructor"` passaria por um objeto.
- **A condição escolhida decide o que o clique marca**: de face, as faces (o número é só rótulo); de dente inteiro, o número (as faces ficam `aria-disabled`). Cada `Dente` recebe `onFace` e `onNumero` só quando valem, então a ausência da ação é o estado inativo, sem prop de "desativado".
- **O símbolo é da condição, não da tela.** `desenho.tsx` tem um `Record` por escopo (`CondicaoDeFace` e `CondicaoDeDente`): uma décima condição não compila sem símbolo, e o teste reprova símbolo repetido. O mesmo `IconeDaCondicao` aparece na barra e na `Legenda`, que passa a dizer a verdade sobre o desenho.
- **As marcas têm `pointer-events: none`**: o clique atravessa o símbolo do dente inteiro (o anel da coroa cobre as faces) e chega na face. Conferido com clique real no Chrome.
- **Uma região `role="status"` avisa o resultado do clique.** O nome da face muda, mas leitor de tela nem sempre lê o nome novo de um elemento que já estava focado.
- **A barra é `radio` nativo**, como o seletor de dentição (item 3.6): setas e anúncio do grupo vêm do navegador. A escolhida se destaca por contorno e realce, e não por cor de fundo, para não esconder as cores das condições.
- **O módulo só tem `abaPaciente`** (`rotas: []`, sem coluna): o odontograma vive dentro da ficha, que lê as abas do registro sem conhecê-las.

## ⚠️ Armadilhas e aprendizados

- **Objeto indexado por texto de entrada aceita nome herdado**: `CONDICAO_POR_ID["constructor"]` devolve uma função. Por isso a validação usa `find` sobre a lista, e o teste cobre exatamente esse valor.
- **`fill-current` e `stroke-current` herdam a cor do grupo**: a classe `text-*` da condição vai no `<g>` da marca, e a forma-base da face define o próprio `stroke`, então continua cinza. Trocar a cor de uma condição é trocar o literal em `condicoes.ts`.
- **`Legenda` mudou**: o quadrado colorido virou o ícone com o símbolo, e o teste mudou junto. A nota [[CondicoesELegenda]] e o glossário ainda dizem que os símbolos estão `<A DEFINIR>`; a tabela deles está em [[MarcarCondicoes]].
- **Nada apaga o odontograma quando o paciente é excluído** (o módulo de pacientes ainda não exclui). Quem implementar a exclusão precisa remover a entrada de `odontogramas` — é dado de saúde.
- **160 paradas de `Tab` nas faces** e, com condição de dente inteiro escolhida, mais 32 nos números. Navegar por setas, com um `Tab` por arcada, ficou de fora.
- **Conferência feita em Chrome headless**: o app real (ficha, aba na ordem Dados, Odontograma, Tratamentos), clique real sobre uma face coberta pelo símbolo do dente inteiro, `Espaço` sem rolar a página, marcas depois de recarregar, outro paciente sem marcas, tema claro e escuro e 390 px sem rolagem horizontal da página. Não foi conferido: toque num aparelho de verdade e leitor de tela.

## 🧪 Como testar

1. `npx vitest run src/modulos/odontograma` passa: a regra de marcar em `marcas.test.ts` (aplicar, remover, substituir na face, somar no dente inteiro, não mutar) e a validação (dente, face e condição inválidos); `dados.test.ts` (guardar, remover, um odontograma por paciente, recusar sem gravar, o hook); `desenho.test.tsx` (nenhum símbolo repete); `Dente`, `Odontograma`, `Legenda`, `AbaOdontograma` (o fluxo inteiro) e `modulo.test.ts` (a aba, ordem 20).
2. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam, e as classes novas (`fill-current`, `stroke-current`, `peer-checked:ring-primary-700`, as cores das condições) estão no CSS do build.
3. No app (`npm run dev`), abra `/pacientes`, um paciente e a aba Odontograma. Com Cárie, clique numa face: ela é tingida e ganha um círculo vermelho; clique de novo e some. Escolha Restauração e clique na mesma face: substitui. Escolha Coroa e Tratamento de canal e clique no número do 36: o dente ganha um anel e uma linha. Recarregue a página: as marcas voltam. Abra outro paciente: sem marcas. Com `Tab` chegue a uma face e marque com `Enter` ou `Espaço` (a página não rola).
4. Confira na legenda e na barra que cada condição tem um desenho diferente, com o tema claro e o escuro.

## 📎 Documentação afetada

- [[MarcarCondicoes]]
- [[CondicoesELegenda]]
- [[Arcadas]]
- [[Dente]]
- [[ADR-003-modulos-por-pasta-com-registro-automatico]]
- [[2026]] (changelog)
