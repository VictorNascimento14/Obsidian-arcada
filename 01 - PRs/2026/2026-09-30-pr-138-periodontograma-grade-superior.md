---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 138
url: https://github.com/VictorNascimento14/Arcada/pull/138
branch: feat/perio-superior
tags: [pr, periodontograma, grade, ficha]
status: merged
---

# PR #138 — feat(periodontograma): registrar a sondagem da arcada superior na ficha do paciente

## 🎯 Contexto

Item 4.2 (Grade da arcada superior) do [[2026-09-30-plano-da-v1]]. Usa o modelo do item 4.1 (PR #88: `exame.ts`, `SITIOS`, `MedidaSitio`) e a notação FDI de `src/dominio/fdi.ts`; a aba entra pela `abaPaciente` que a ficha lê de `NAVEGACAO.abasPaciente`. A arcada inferior, o teclado, o sangramento e os índices vêm nos itens 4.3 a 4.6. Fecha a issue #128.

## 🔧 Mudanças

- `src/modulos/periodontograma/grade.ts` (+ teste): `rotuloDoSitio` e `nomeDoSitio` (palatino na arcada superior, lingual na inferior), `CAMPOS_DE_MEDIDA` (rótulo e intervalo de cada campo), `medidaValida` e `faixaDoCampo`.
- `src/modulos/periodontograma/dados.ts` (+ teste): a coleção `exames-perio` (`ExameSalvo`: `id`, `pacienteId`, `data`, `dentes`), `useExameDoDia` e `registrarMedida`.
- `src/modulos/periodontograma/GradeDeSondagem.tsx`: a tabela de uma arcada, uma linha por dente e uma coluna por sítio; só desenha, quem grava é o `aoMedir`.
- `src/modulos/periodontograma/AbaPeriodonto.tsx` (+ teste): a aba, com o título do exame de hoje, o aviso de valor recusado e a grade da arcada superior.
- `src/modulos/periodontograma/modulo.ts` (+ teste): só a `abaPaciente` (ordem 30, rótulo `Periodonto`), sem rota e sem item na coluna.

## 🕵️ Dado pessoal (LGPD)

O exame periodontal é dado de saúde, sensível pela LGPD. Fica só no `localStorage` do navegador, na coleção `exames-perio` de `src/dados/`, sem envio a lugar nenhum. Os pacientes dos testes são fictícios e sem CPF.

## 🧠 Decisões técnicas

- **Um exame por paciente por dia.** O exame de hoje nasce na primeira medida e é editado no lugar; os de outros dias ficam como estavam, que é a base da comparação do item 4.7. A aba abre sempre o de hoje, então em outro dia ela começa em branco (o texto do cabeçalho diz isso). Teto conhecido: não há como editar um exame antigo nem ter dois no mesmo dia.
- **Grava a cada valor digitado, sem botão de salvar.** `registrarMedida` é a regra: exige paciente existente, dente da FDI e valor inteiro (profundidade de 0 a 15, margem de −15 a 15); a tela é só conforto — valor recusado devolve o campo ao que estava e mostra o aviso. Digitar o mesmo valor de novo, ou apagar um campo que já está vazio, não grava nem cria o exame.
- **Rótulo do sítio por arcada, nome do modelo intacto.** O modelo segue com ML, L e DL; a tela troca por MP, P e DP na arcada superior (`rotuloDoSitio`), e o nome por extenso (`nomeDoSitio`, palatino ou lingual) vai no `aria-label` de cada campo.
- **A margem se rotula com o sinal**, no cabeçalho do grupo e no nome de cada campo: `Margem (+ recessão, − coronal)`. É o que a nota [[2026-09-30-margem-gengival-positiva-e-recessao]] pedia da grade.
- **Uma linha por dente, uma coluna por sítio**, com a profundidade dos seis sítios e depois a margem. Cabe em cerca de 550 px e, no celular, rola na horizontal com a coluna do dente e o título fixos. O desenho clássico, com os dentes em colunas, passaria de 1.700 px e pediria espelhar a ordem dos sítios de cada lado da boca.
- **A aba tem `key` por paciente**: a ficha reaproveita a aba ao trocar de paciente, e sem a `key` o aviso de um apareceria na tela do outro; há teste.

## ⚠️ Armadilhas e aprendizados

- O campo é `type="number"`, e o navegador devolve `""` para o `-` solto: a tela o trata como campo apagado até o dígito chegar. Por isso a margem não leva `inputMode`: o teclado numérico do celular não tem o sinal de menos.
- No Chrome, a roda do mouse sobre um campo numérico em foco muda o valor. Numa grade de 192 campos isso estragaria medidas em silêncio; o `onWheel` solta o foco (há teste).
- Cada valor digitado regrava a coleção `exames-perio` inteira no `localStorage`. Um exame de 32 dentes é pequeno (dezenas de KB no máximo); se pesar, gravar no `onBlur`.
- Dente ausente ainda não se marca na grade: o modelo ignora `ausente`, mas nenhum dos itens 4.2 a 4.6 traz o controle. Dente sem medida não entra nos índices de qualquer forma.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/periodontograma` — os rótulos e intervalos (`grade.test.ts`), a gravação (`dados.test.ts`), a tela (`AbaPeriodonto.test.tsx`) e o registro da aba (`modulo.test.ts`).
2. `npm run dev`, abrir um paciente em `/pacientes` e escolher a aba **Periodonto**: aparecem os 16 dentes da arcada superior, do 18 ao 28, com os sítios MV, V, DV, MP, P e DP sob **Profundidade** e sob **Margem (+ recessão, − coronal)**.
3. Digitar 3 na profundidade do MV do dente 16 e −2 na margem: os valores ficam. Recarregar a página e conferir que continuam (chave `arcada:exames-perio`).
4. Digitar 20 numa profundidade: o campo volta ao valor anterior e aparece `Profundidade: use um número inteiro de 0 a 15 mm.`
5. Com a largura de um celular (390 px), rolar a grade na horizontal: a coluna do dente e o título `Arcada superior` ficam parados.

## 📎 Documentação afetada

- [[GradeDeSondagem]]
- [[2026-09-30-margem-gengival-positiva-e-recessao]]
- [[glossario]]
- [[2026]] (changelog)
