---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 121
url: https://github.com/VictorNascimento14/Arcada/pull/121
branch: feat/procedimentos-reajuste
tags: [pr, procedimentos, preco, reajuste]
status: merged
---

# PR #121 — feat(procedimentos): reajustar preços em lote com prévia

## 🎯 Contexto

Item 6.4 (Reajuste de preços em lote) do [[2026-09-30-plano-da-v1]], sobre a lista com busca e filtro (PR #81) e o cadastro e edição de procedimento (PR #96). O arredondamento repete o cuidado do desconto do orçamento (PR #68, `tratamentos/desconto.ts`): percentual em centésimos inteiros, sem ponto flutuante — ver [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]]. Fecha a issue #109.

## 🔧 Mudanças

- `src/modulos/procedimentos/reajuste.ts` (+ teste): `lerPercentual`, `reajustarPreco` e `aplicarReajuste`.
- `src/modulos/procedimentos/ReajusteDePrecos.tsx` (+ teste): o modal com o percentual, a prévia e a confirmação (`Modal` do kit).
- `src/modulos/procedimentos/ListaProcedimentos.tsx` (+ teste): a caixa de seleção por linha, «Selecionar todos» e o botão «Reajustar preços».

## 🧠 Decisões técnicas

- **Novo preço = preço × (1 + percentual ÷ 100), arredondado para o centavo mais próximo; o meio centavo sobe.** Em inteiros: o percentual vira centésimos (`Math.round(percentual * 100)`) e a conta é `Math.round(preco * (10_000 + centésimos) / 10_000)`. Em ponto flutuante, R$ 57,00 a +0,5% daria 5728,4999… (desce para 5728) e R$ 42,50 a -6,2% daria 3986,4999… (desce para 3986); em inteiros dão 5729 e 3987, e os dois casos estão no teste. O percentual vale com até duas casas.
- **O meio centavo sobe no preço final**, no aumento e na redução: arredondar o preço novo dá o mesmo que somar o arredondamento da diferença, e o preço nunca fica negativo.
- **De -100% a +1000%.** -100 zera o preço (o cadastro já aceita preço zero); abaixo disso ficaria negativo; acima de 1000 é engano de digitação, e a conta deixaria de ser exata. `lerPercentual` recusa o que está fora, e `aplicarReajuste` lança `RangeError`.
- **Tudo ou nada.** `aplicarReajuste` grava com `substituirTudo`, uma escrita só, em vez de um `salvar` por procedimento: ou todos os preços mudam, ou nenhum. Devolve quantos preços mudaram (o que o arredondamento deixa como estava não conta).
- **A prévia é da tela e a gravação é da regra.** O modal calcula a prévia com `reajustarPreco` e só chama `aplicarReajuste` em «Aplicar reajuste», que fica desabilitado sem percentual válido e quando nenhum preço mudaria (percentual zero, ou preço pequeno demais).
- **A seleção sobrevive ao filtro, mas o reajuste só alcança o que se vê marcado.** Quem marcou e depois filtrou não reajusta, sem ver, o que o filtro escondeu.
- **Só o `preco` da tabela muda.** O item de um plano já montado guarda a própria cópia do preço (`ItemPlano.preco`), então o reajuste vale para os itens novos.

## ⚠️ Armadilhas e aprendizados

- O campo do percentual não tem `inputMode="decimal"` de propósito: o teclado numérico do iOS não traz o sinal de menos, e o percentual pode ser negativo.
- O empate de meio centavo só aparece em contas exatas, e o ponto flutuante o erra de forma silenciosa: por isso o teste fixa dois casos que a conta com `1 + percentual / 100` errava, um de aumento e um de redução.
- Sem verificação visual (o navegador de teste não conecta nesta máquina): a lista e o modal só usam classes escritas por extenso no fonte, que o JIT do Tailwind gera, e componentes do kit (`Modal`, `TextField`, `Button`).

## 🧪 Como testar

1. `npm run dev`, abra `/procedimentos`: o botão «Reajustar preços» está desabilitado. Marque dois procedimentos: o botão habilita e a contagem passa a dizer «· 2 selecionados».
2. Busque `resina` e marque «Selecionar os visíveis»: só o que aparece fica marcado. Limpe a busca: a marca continua, e a contagem mostra o total de selecionados.
3. Abra «Reajustar preços» e digite `10`: a prévia lista os selecionados, cada um com o preço atual e o novo (10% a mais). Nada foi gravado: clique em «Cancelar» e a lista mostra os mesmos preços.
4. Abra de novo, digite `-10,5` e clique em «Aplicar reajuste»: aparece «N preços reajustados», a lista mostra os preços novos e a seleção é limpa. Os procedimentos que não estavam marcados não mudam.
5. Digite `abc`, `-101` ou `0` no percentual: o botão «Aplicar reajuste» fica desabilitado, com a mensagem de erro (ou «Nenhum preço muda com este percentual.»).
6. `npx vitest run --maxWorkers=2 src/modulos/procedimentos` — regra, modal e lista.

## 📎 Documentação afetada

- [[ReajusteDePrecos]]
- [[ListaDeProcedimentos]]
- [[2026-09-30-plano-da-v1]]
- [[2026-09-30-percentual-em-ponto-flutuante-erra-o-meio-centavo]]
- [[2026]] (changelog)
