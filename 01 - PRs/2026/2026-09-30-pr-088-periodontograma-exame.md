---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 88
url: https://github.com/VictorNascimento14/Arcada/pull/88
branch: feat/perio-exame
tags: [pr, periodontograma, exame]
status: aberto
---

# PR #88 — feat(periodontograma): modelar o exame de seis sítios e calcular os índices

## 🎯 Contexto

Item 4.1 (Modelo de seis sítios e cálculos) do [[2026-09-30-plano-da-v1]], o primeiro do módulo Periodontograma. Puro, sem tela e sem `modulo.ts`: a aba “Periodonto” na ficha vem com a grade (4.2). Usa a notação FDI (`src/dominio/fdi.ts`, [[ADR-004-notacao-fdi-no-odontograma]]) para saber quais números de dente existem. Fecha a issue #83.

## 🔧 Mudanças

- `src/modulos/periodontograma/exame.ts` (+ teste): `SITIOS` e os tipos `Sitio`, `MedidaSitio`, `DentePerio` e `ExamePerio`; `nivelDeInsercao`; `indicesDoExame` e o tipo `IndicesPerio`.

## 🕵️ Dado pessoal (LGPD)

Este PR não guarda dado de ninguém: define o modelo e as contas. O exame — dado de saúde, sensível pela LGPD — passa a ser gravado no item 4.2, na coleção `exames-perio`. Os números dos testes são medidas inventadas, sem paciente.

## 🧠 Decisões técnicas

- **Margem positiva é recessão, e o nível de inserção é a soma simples.** Com a margem contada da junção esmalte-cemento, a recessão sobe o nível e a margem coronal (negativa) o desconta: `nível = profundidade + margem`, sem caso especial. É a definição do glossário (“somando a recessão à profundidade”); o risco de inverter o sinal está em [[2026-09-30-margem-gengival-positiva-e-recessao]].
- **O nível de inserção não se guarda.** Sai de `nivelDeInsercao(medida)`: não há um terceiro número para ficar fora de sincronia com a profundidade e a margem.
- **Medidas opcionais; sítio medido é o que tem profundidade.** A grade é preenchida aos poucos (a profundidade de todos os sítios antes das margens, por exemplo). O sítio só entra nos índices com profundidade: sem ela, o sangramento anotado fica de fora, e por isso o percentual nunca passa de 100.
- **A inserção só existe com profundidade e margem.** Sem a margem o app não presume zero: o sítio fica fora do contador de inserção, em vez de contar como se não houvesse recessão.
- **Sem sítio medido, percentual e média são `null`, não zero.** “0%” e “0 mm” diriam que o exame deu normal quando ele ainda está vazio; como mostrar (um traço, por exemplo) é da tela.
- **Percentual e média saem exatos, sem arredondar.** Arredondar e formatar (uma casa, vírgula) é da borda. O percentual é `sangrando * 100 / medidos`: a divisão de dois inteiros arredonda uma vez só, e `(a / b) * 100` arredonda duas.
- **O exame é um mapa por número FDI**, e só entram na conta os números que `denteValido` reconhece (permanentes e decíduos); qualquer outra chave é ignorada. Dente `ausente` fica fora mesmo com medida registrada: marcar a ausência depois não obriga a apagar o que foi digitado.
- **Os limites (4 mm e 3 mm) são fixos e estão no nome dos campos** (`sitiosComProfundidade4mmOuMais`, `sitiosComInsercao3mmOuMais`), como definidos no plano; não há configuração.

## ⚠️ Armadilhas e aprendizados

- **O sinal da margem não dá erro quando está trocado.** Quem digita “recessão de 2 mm” tem de entrar `2`, não `-2`; um sinal invertido só faz o nível de inserção descer onde devia subir. O rótulo do campo na grade (4.2) precisa dizer isso.
- `NumeroDente` é só `number`, então `ExamePerio` aceita qualquer chave numérica no tipo. Quem barra o dente que não existe é `denteValido`, dentro de `indicesDoExame` (o teste usa o 19).
- As chaves de um objeto viram texto (`"16"`) quando o exame passa pelo `localStorage`; `Object.keys(...).map(Number)` devolve o número FDI, e é por ele que se consulta o dente.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/periodontograma` — `nivelDeInsercao` (a recessão soma, a margem coronal desconta, falta de medida) e `indicesDoExame` (os quatro índices num dente de seis sítios, dois dentes de arcadas diferentes, dente ausente, número fora da FDI, sítio sem profundidade e exame sem sítio medido).
2. `npm run type-check` — o exame é tipado por número de dente e por sítio (`MV`, `V`, `DV`, `ML`, `L`, `DL`).

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[glossario]]
- [[ADR-004-notacao-fdi-no-odontograma]]
- [[2026-09-30-margem-gengival-positiva-e-recessao]]
- [[2026]] (changelog)
