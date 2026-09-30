---
tipo: adr
numero: 4
data: 2026-09-30
status: aceito
tags: [adr, dominio, odontograma]
---

# ADR-004 — Notação FDI no odontograma

## Contexto

O odontograma, o periodontograma, o plano de tratamento e o atendimento precisam nomear cada dente sem
ambiguidade — na tela, no dado guardado e no impresso. Há mais de uma numeração em uso no mundo:

- a **notação FDI** (ISO 3950) usa dois dígitos: o primeiro é o quadrante; o segundo, a posição contada a
  partir da linha média;
- a **numeração universal americana** dá aos dentes permanentes um número de 1 a 32 (e letras aos
  decíduos).

## Decisão

1. **O dente é identificado pelo número FDI de dois dígitos.** Permanentes: `11`–`18`, `21`–`28`,
   `31`–`38` e `41`–`48`. Decíduos: `51`–`55`, `61`–`65`, `71`–`75` e `81`–`85`.
2. O primeiro dígito é o **quadrante**: 1 superior direito, 2 superior esquerdo, 3 inferior esquerdo,
   4 inferior direito; de 5 a 8 repetem a ordem nos decíduos. O segundo é a **posição** a partir da linha
   média: 1 é o incisivo central; nos permanentes vai até 8 (terceiro molar) e nos decíduos até 5
   (segundo molar). Assim, `11` é o incisivo central superior direito e `36`, o primeiro molar inferior
   esquerdo.
3. As **faces** são **V** (vestibular), **L** (lingual) ou **P** (palatina), **M** (mesial), **D** (distal)
   e **O** (oclusal) ou **I** (incisal): cinco por dente. L vale para os dentes inferiores e P para os
   superiores; I vale para os dentes da frente (incisivos e caninos) e O para os de trás.
4. A regra da notação — que números existem e a que quadrante e dentição cada um pertence — mora em
   `src/dominio/`, com teste.
5. **Por que FDI e não a numeração universal (1–32): é a usada no Brasil.** O número que o profissional já
   usa na ficha de papel e no orçamento é o mesmo que o app mostra e guarda; adotar a outra exigiria
   converter a cada tela. A FDI ainda carrega quadrante e posição no próprio número e nomeia decíduos e
   permanentes no mesmo esquema de dois dígitos.

## Consequências

**A favor**

- Uma só notação em tela, dado, impresso e orçamento: o que o profissional lê é o que o app guarda.
- O número já diz o quadrante e a dentição (de 1 a 4, permanente; de 5 a 8, decídua): o código deriva as
  duas coisas do próprio número.
- Dentição mista sem esforço: os números dos dois conjuntos convivem no mesmo odontograma.

**Custos**

- **Os números não são contínuos** (`11`–`18`, depois `21`–`28`…): número FDI não serve de índice de
  lista. Iteração e ordenação saem de uma lista definida em `src/dominio/`.
- **Quem trabalha com a numeração universal** vai ver números diferentes; a v1 não oferece outra notação.
- **O número FDI vira parte do dado guardado.** Mudar de notação depois exigiria migrar tudo o que já foi
  registrado (odontogramas, planos de tratamento, evoluções).

Os termos (quadrante, dentição, faces) estão em [[glossario]]. O módulo que usa a notação primeiro é o
odontograma; ver [[2026-09-30-plano-da-v1]].

## Implementado em

- Os tipos `NumeroDente` e `Face`: [[2026-09-30-pr-025-tipos-do-dominio]], em `src/dominio/odontologia.ts`; visão geral em
  [[TiposDoDominio]].
- A regra da notação (números que existem, quadrante e dentição): TODO: PR que implementar (item 3.1 do
  [[2026-09-30-plano-da-v1]]).
