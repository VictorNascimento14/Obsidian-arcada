---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 53
url: https://github.com/VictorNascimento14/Arcada/pull/53
branch: feat/odontograma-fdi
tags: [pr, odontograma, dominio]
status: merged
---

# PR #53 — feat(odontograma): definir a notação FDI com dentes, quadrantes e tipos

## 🎯 Contexto

Item 3.1 do [[2026-09-30-plano-da-v1]] (módulo 3, Odontograma): a regra da notação de [[ADR-004-notacao-fdi-no-odontograma]], que os tipos `NumeroDente` e `Face` de [[2026-09-30-pr-025-tipos-do-dominio]] deixaram para este PR. É a base dos itens seguintes do módulo, do periodontograma (4) e do plano de tratamento (7). Fecha a issue #43.

## 🔧 Mudanças

- `src/dominio/fdi.ts` — `DENTES_PERMANENTES` e `DENTES_DECIDUOS` (por arcada, na ordem de exibição), `denteValido`, `quadrante`, `ehDeciduo`, `arcada`, `lado`, `tipoDente` (com `TIPOS_DENTE`, o rótulo de cada tipo) e `nomeDente`.
- `src/dominio/fdi.test.ts` — as listas, a validade, a derivação e os nomes; os 52 nomes são únicos.
- `src/dominio/index.ts` — reexporta `fdi.ts`: a regra se importa de `@/dominio`.
- [[NotacaoFdi]] — nota nova de funcionalidade no cofre.

## 🧠 Decisões técnicas

- **Tudo sai do número; nada é guardado à parte.** Quadrante, dentição, arcada, lado e tipo são derivados de `n` por tabelas pequenas (`POR_QUADRANTE` e os tipos por posição), como pede o [[ADR-004-notacao-fdi-no-odontograma]]: o dado guardado é só o número.
- **As listas de exibição são a fonte do que existe.** `denteValido` confere contra elas, e não contra uma conta (`10 < n < 49`), que aceitaria o 19 e o 29.
- **Dente que não existe lança `RangeError`** em `quadrante` e nos derivados, em vez de devolver um valor plausível (a conta do primeiro dígito daria "quadrante 1" para o 19). `denteValido` é a versão que não lança, para conferir dado que vem de fora.
- **`denteValido` devolve `boolean`, não um predicado de tipo** (`n is NumeroDente`): como `NumeroDente` é `number`, o ramo falso de um predicado estreitaria um `number` inválido para `never`. Aceita `unknown` para servir a dado guardado ou digitado.
- **O tipo vem de uma tabela por posição, uma por dentição**, e não de `if` por faixa. O ordinal do nome (primeiro, segundo, terceiro) sai de contar quantos dentes do mesmo tipo vêm antes na tabela — sem caso especial por tipo.
- **`TIPOS_DENTE` traz o rótulo de cada tipo** (chave sem acento, como `FAIXAS_ETARIAS`), e `nomeDente` o usa.
- **As listas são por arcada, da esquerda para a direita de quem olha o paciente**: o desenho só as percorre, e a linha média é o meio da lista.

## ⚠️ Armadilhas e aprendizados

- Os números não são contínuos: depois do `18` vem o `21`. `n + 1` e `n - 1` não andam pela boca; iterar e ordenar é pelas listas.
- No decíduo, as posições 4 e 5 são **molares**, não pré-molares: `54` é o primeiro molar decíduo. Quem supõe pré-molar em toda dentição erra o nome — e, no item seguinte, a face (incisal ou oclusal).
- Direito e esquerdo são **do paciente** ([[glossario]]): o `11`, direito do paciente, desenha-se à esquerda da linha média para quem olha.

## 🧪 Como testar

1. `npx vitest run src/dominio/fdi.test.ts` passa: as listas na ordem de exibição (32 permanentes e 20 decíduos, sem repetição), os números que existem e os que não existem (19, 56, 90, texto, decimal), quadrante, dentição, arcada, lado e tipo de um dente de cada quadrante, e os nomes — os 52 são diferentes entre si.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.
3. Conferir à mão duas respostas do ADR-004: `nomeDente(16)` é `primeiro molar superior direito` e `nomeDente(75)` é `segundo molar decíduo inferior esquerdo`.

## 📎 Documentação afetada

- [[ADR-004-notacao-fdi-no-odontograma]]
- [[NotacaoFdi]]
- [[TiposDoDominio]]
- [[2026]] (changelog)
