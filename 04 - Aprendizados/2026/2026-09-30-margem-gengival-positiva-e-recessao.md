---
tipo: aprendizado
data: 2026-09-30
contexto: PR #88 — modelo do exame periodontal
tags: [aprendizado, periodontograma, convencao]
---

# Margem gengival positiva é recessão

Origem: [[2026-09-30-pr-088-periodontograma-exame]].

## O sintoma

Dois números descrevem a gengiva num sítio — a profundidade de sondagem e a posição da margem — e o nível de inserção clínica é a conta entre os dois. Se a tela grava a margem com um sinal e a conta espera o outro, o nível sai errado sem erro nenhum: sobe onde devia descer.

## A causa

A margem gengival é medida a partir da junção esmalte-cemento, e ela pode estar de dois lados: apical à junção (a raiz aparece: recessão) ou coronal a ela (a gengiva cobre parte da coroa). Um único campo tem de escolher qual lado é o positivo, e a escolha muda a conta.

## A regra do Arcada

**Positiva = recessão** (margem apical à junção); **negativa = margem coronal**. Com isso o nível de inserção clínica é a soma simples, `profundidade + margem`, sem caso especial. Profundidade de 3 mm com recessão de 2 mm dá 5 mm; profundidade de 4 mm com a margem 1 mm coronal (−1) dá 3 mm. É a definição do glossário do cofre (“somando a recessão à profundidade”).

A conta mora em `nivelDeInsercao`, em `src/modulos/periodontograma/exame.ts`, e o teste cobre os dois sinais.

## Como evitar

- O rótulo do campo da margem na grade tem de dizer o sinal (“recessão +, margem coronal −”): quem digita “recessão de 2 mm” entra `2`. É o que a grade do item 4.2 precisa fazer.
- Medida que venha de fora (importação de outro sistema) tem o sinal conferido antes de entrar: outra fonte pode contar a margem no sentido oposto.
- Veja [[glossario]] (recessão gengival e nível de inserção clínica) e [[ADR-004-notacao-fdi-no-odontograma]] para os dentes.
