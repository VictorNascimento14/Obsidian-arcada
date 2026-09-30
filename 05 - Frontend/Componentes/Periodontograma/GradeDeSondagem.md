---
tipo: funcionalidade
camada: frontend
area: Periodontograma
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, periodontograma, grade]
---

# Grade de sondagem

## O que é

A aba **Periodonto** da [[FichaDoPaciente]]: o exame periodontal de hoje, com a profundidade de sondagem e a
margem gengival de cada um dos seis sítios de cada dente. É o item 4.2 do [[2026-09-30-plano-da-v1]] e usa o
modelo do PR [[2026-09-30-pr-088-periodontograma-exame]]. Não tem rota própria: o módulo só registra a aba
(`abaPaciente`, ordem 30), e a ficha a monta quando ela é escolhida. Os termos estão no [[glossario]]
(Periodontograma).

## Onde está no código

- `src/modulos/periodontograma/modulo.ts` — só a `abaPaciente`: ordem 30, rótulo `Periodonto`.
- `src/modulos/periodontograma/AbaPeriodonto.tsx` — a aba (exportação padrão, recebe `pacienteId`): cabeçalho,
  aviso e a grade.
- `src/modulos/periodontograma/GradeDeSondagem.tsx` — a tabela de uma arcada; só desenha, quem grava é o `aoMedir`.
- `src/modulos/periodontograma/grade.ts` — os rótulos dos sítios por arcada, o rótulo e o intervalo de cada campo
  e `medidaValida`.
- `src/modulos/periodontograma/dados.ts` — a coleção `exames-perio`, `useExameDoDia` e `registrarMedida`.

## Comportamento

- **O exame é do dia.** O título diz `Exame periodontal de DD/MM/AAAA` (hoje, por `diaISO`). O exame de hoje nasce
  na primeira medida registrada e é editado no lugar durante o dia; em outro dia a aba abre em branco, e os
  exames dos dias anteriores ficam guardados como estavam (a comparação é o item 4.7).
- **A grade**: uma linha por dente permanente da arcada superior, na ordem em que se desenham (18 a 11 e 21 a 28),
  e uma coluna por sítio. Primeiro o grupo **Profundidade** com os seis sítios, depois o grupo **Margem (+ recessão,
  − coronal)**.
- **Rótulos dos sítios**: o modelo chama de ML, L e DL os sítios de dentro da boca; na arcada superior a tela
  mostra **MP, P e DP** (palatino). Cada campo tem o nome por extenso para leitor de tela: `Profundidade, dente 16,
  mesiopalatino`.
- **O sinal da margem**: positiva é recessão, negativa é margem coronal. O rótulo do grupo e o nome de cada campo
  dizem isso, porque sinal trocado não dá erro — só nível de inserção errado (ver
  [[2026-09-30-margem-gengival-positiva-e-recessao]]).
- **Entrada**: número inteiro, em mm. Profundidade de 0 a 15; margem de −15 a 15. O que se digita é gravado na
  hora, sem botão de salvar. Apagar o campo apaga a medida; `0` é medida, não campo vazio.
- **Valor que não serve** (fora do intervalo ou com decimais) não é gravado: o campo volta ao valor anterior e o
  aviso abaixo do título diz o intervalo (`Profundidade: use um número inteiro de 0 a 15 mm.`). Um valor bom seguinte
  limpa o aviso.
- **Cada paciente tem o seu exame**: trocar de paciente na mesma tela não leva o aviso nem o que foi digitado.
- **No celular** a tabela rola na horizontal, com a coluna do dente e o título `Arcada superior` parados.
- **Sem sugestão de conduta**: a tela só colhe medidas; os índices são o item 4.6.

## Movimento e micro-interações

Os campos são caixas de 36 px, com o foco por anel (`focus:ring`), sem as setinhas de incremento do navegador. A roda
do mouse sobre um campo em foco solta o foco, para rolar a página nunca mudar uma medida. Nada mais anima.

## Limites conhecidos

- **Só a arcada superior**: a inferior e a navegação por setas são o item 4.3; sangramento e supuração, o 4.4.
- **Não dá para editar um exame de outro dia** nem ter dois no mesmo dia: a aba abre sempre o de hoje.
- **Dente ausente** ainda não se marca na grade: o modelo ignora `ausente`, mas a tela não tem o controle. Dente sem
  medida não entra nos índices de qualquer forma.
- **Milímetro inteiro**: a sonda é graduada assim; meio milímetro pediria mudar `medidaValida` e o `step` do campo.

## Histórico de mudanças

- [[2026-09-30-pr-138-periodontograma-grade-superior]] — a grade da arcada superior, com gravação no exame do dia.
