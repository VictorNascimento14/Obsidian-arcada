---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 102
url: https://github.com/VictorNascimento14/Arcada/pull/102
branch: feat/agenda-calendario
tags: [pr, agenda, calendario, visao-do-dia]
status: merged
---

# PR #102 — feat(agenda): mostrar o calendário do mês com os dias que têm consulta

## 🎯 Contexto

Item 8.8 do [[2026-09-30-plano-da-v1]] (módulo 8 · Agenda). Apoia-se na visão do dia ([[Agenda do dia]]) e no marcar consulta ([[Marcar consulta]]), que já estão na `main`. Fecha a issue #98.

## 🔧 Mudanças

- `src/modulos/agenda/CalendarioDoMes.tsx` e `CalendarioDoMes.test.tsx` — o `Calendar` do kit com `marcados`; clicar num dia chama `aoAbrirDia`.
- `src/modulos/agenda/dias.ts` e `dias.test.ts` — `diasComConsulta`: os dias com consulta na grade, sem repetir e em ordem; a cancelada não conta.
- `src/modulos/agenda/PaginaAgenda.tsx` — o calendário ao lado da grade (`xl`) ou atrás do botão **Mês**; no celular, o título sobe para a linha de cima e o botão de marcar vira ícone.

## 🧠 Decisões técnicas

- **O mês do calendário é dele.** O `Calendar` do kit guarda o mês em estado próprio e não aceita mês controlado; como `src/ui/` não se edita, ele não acompanha o dia aberto. Anda pelas setas dele, e clicar num dia só troca o dia da grade.
- **Marca o que a grade mostra.** `diasComConsulta` ignora a cancelada, que libera o horário e sai da grade; a que faltou continua marcando. O dia de hoje aparece na pílula verde do kit, que tem prioridade sobre a marca.
- **Ao lado em `xl`, atrás do botão Mês abaixo disso.** Com a coluna lateral aberta, abaixo de 1280 px a grade de duas cadeiras perde largura. O calendário é `sticky` e acompanha a rolagem do dia. `hidden`, `block` e os `xl:` são classes literais no fonte.
- **Título em cima e `+` no celular.** Com o botão Mês, os controles não cabiam com o título numa linha (o título quebrava em cinco). O título ocupa a linha de cima e o botão de marcar mostra só o ícone; o texto fica em `sr-only`, então o nome acessível não muda.

## ⚠️ Armadilhas e aprendizados

- O `Calendar` do kit não marca o dia aberto: hoje é a única pílula cheia. O dia que a grade mostra aparece no título acima dela.
- O ponto de um dia marcado é o único `<span>` dentro do botão do dia: o teste o procura por aí, já que o kit não dá `aria` de marcado.
- Conferido só no tema escuro, num Chrome headless sobre o `vite preview`, em 1440 e 390 de largura (não há navegador conectado nesta máquina).

## 🧪 Como testar

1. `npm run dev` e abra `/agenda` numa janela larga (1280 px ou mais): o calendário do mês aparece à esquerda da grade, com o dia de hoje em verde e um ponto nos dias que têm consulta.
2. Clique num dia com ponto: o título e a grade passam a ser desse dia. As setas do calendário mudam o mês sem mudar o dia aberto.
3. Estreite a janela (ou abra no celular): o calendário some e o botão **Mês** o abre e fecha; escolher um dia o fecha e abre o dia. O título do dia fica na linha de cima, e **Marcar consulta** aparece como `+`.
4. Marque uma consulta (**Marcar consulta**) num dia que não tinha nenhuma: o ponto aparece nesse dia do calendário.
5. `npx vitest run --maxWorkers=2 src/modulos/agenda` roda os testes da agenda; `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[Calendário do mês]]
- [[Agenda do dia]]
- [[Marcar consulta]]
- [[2026]] (changelog)
