---
tipo: funcionalidade
camada: frontend
area: Atendimento
rota: /atendimento/:consultaId
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, atendimento, odontograma, procedimentos]
---

# Procedimento realizado no odontograma

## O que é

O elo entre o que foi feito e o que o desenho do dente mostra: ao marcar um item do plano como realizado no
[[ProcedimentosRealizados]], a condição que o procedimento deixa (`condicaoResultante`, do catálogo) é aplicada no
odontograma do paciente. É o item 9.4 do [[2026-09-30-plano-da-v1]]. As condições são as de [[CondicoesELegenda]] e o
odontograma é o de [[MarcarCondicoes]]; o app só aplica o que o profissional registrou como feito.

## Onde está no código

- `src/modulos/atendimento/odontograma.ts` — `marcasDoItem`, `motivoDeNaoCaber` e `aplicarMarcas`.
- `src/modulos/atendimento/realizados.ts` — `registrarRealizados` chama as três; o resultado traz as `marcas`.
- Do módulo do odontograma entram, só importados, `alternarMarcaDoPaciente` e `odontogramas` (`dados.ts`),
  `alternarMarca` e `conferirMarca` (`marcas.ts`) e `CONDICOES` (`condicoes.ts`).

## Comportamento

### O que o item deixa marcado

- **Condição de dente inteiro** (tratamento de canal, coroa, implante, ausente…): uma marca no dente do item.
- **Condição de face** (restauração, selante): uma marca em **cada face** do item. Uma restauração em M, O e D marca
  as três faces.
- **Nada**, e o registro do procedimento vale do mesmo jeito, quando: o procedimento não tem `condicaoResultante` (ou
  ela não está na lista do odontograma), o item não diz o dente, ou a condição é de face e o item não traz face.

### Aplicar não desfaz

`alternarMarcaDoPaciente` **liga e desliga**: chamá-la numa marca que já está lá a remove. Por isso, antes de gravar,
`aplicarMarcas` pergunta à regra do próprio odontograma (`alternarMarca(...).aplicada`) se a alternância ligaria:
se não, a marca já está no desenho e nada é feito. Realizar duas vezes o mesmo tipo de procedimento no mesmo lugar
não apaga a marca nem a duplica. A regra de "mesmo lugar" (a face, ou o dente com a mesma condição) continua sendo só
do módulo do odontograma; o atendimento não a repete.

- **Numa face**, a marca nova substitui a que estava: a cárie da face O vira restauração.
- **No dente inteiro**, a marca se soma às outras: um dente com canal feito e coroa nova fica com as duas.
- Realizar não remove marca nenhuma além da que a regra manda substituir na face.

### Tudo ou nada

Antes de gravar o plano, `registrarRealizados` confere todas as marcas com `conferirMarca`. Se alguma não cabe (dente
ou face que a notação FDI não tem, só possível com dado adulterado), o registro inteiro é recusado com o motivo — `O
procedimento não cabe no odontograma: Dente 99 não existe na notação FDI.` — e **nem o plano nem o odontograma mudam**.
Assim o plano nunca diz "realizado" com o desenho desatualizado por erro.

O aviso do registro diz `O odontograma foi atualizado.` quando houve marca.

## Histórico de mudanças

- [[2026-09-30-pr-159-atendimento-odontograma]] — as marcas do procedimento realizado no odontograma do paciente.
