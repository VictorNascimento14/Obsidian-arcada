---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 159
url: https://github.com/VictorNascimento14/Arcada/pull/159
branch: feat/atendimento-odontograma
tags: [pr, atendimento, odontograma, tratamentos]
status: merged
---

# PR #159 — feat(atendimento): aplicar no odontograma a condição do procedimento realizado

## 🎯 Contexto

Item 9.4 (Realizado atualiza o odontograma) do [[2026-09-30-plano-da-v1]]: se o procedimento tem condição resultante e o item tem dente e face, aplica no odontograma do paciente pela função exportada pelo módulo de odontograma. Fecha a issue #155.

## 🔧 Mudanças

- `src/modulos/atendimento/odontograma.ts` (+ teste): `marcasDoItem` (o que o item deixa marcado), `motivoDeNaoCaber` (a regra de `conferirMarca`) e `aplicarMarcas` (aplica sem desfazer).
- `realizados.ts`: `registrarRealizados` calcula as marcas antes de gravar, recusa o registro se alguma não cabe e, gravado o plano, as aplica; o resultado traz `marcas`.
- `ProcedimentosRealizados.tsx`: o aviso do registro diz `O odontograma foi atualizado.` quando houve marca.

## 🕵️ Dado pessoal (LGPD)

O odontograma é dado de saúde do paciente, guardado só no repositório local. A marca sai do procedimento que o próprio profissional registrou como feito; o app não decide condição nem tratamento por conta própria.

## 🧠 Decisões técnicas

- `alternarMarcaDoPaciente` liga e desliga, então aplicar duas vezes desfaria a marca. Antes de gravar, `aplicarMarcas` pergunta à regra do próprio odontograma (`alternarMarca(...).aplicada`) se a alternância ligaria; se não, a marca já está lá e nada é feito. A regra de "mesmo lugar" continua sendo só do módulo do odontograma.
- Só marca quando dá para dizer onde: procedimento sem condição (ou com condição que a lista não conhece), item sem dente e condição de face sem faces não mudam o odontograma. O registro do procedimento vale do mesmo jeito.
- É tudo ou nada: se alguma marca não cabe (dente ou face que a notação FDI não tem, só possível com dado adulterado), o registro inteiro é recusado com o motivo, e nem o plano nem o odontograma mudam. Assim o plano nunca diz "realizado" com o desenho desatualizado por erro.
- Numa face a marca nova substitui a que estava (a cárie vira restauração, regra do odontograma); no dente inteiro ela se soma às outras. Realizar não remove marca nenhuma além da que a regra manda substituir.
- O módulo do odontograma não foi editado: só importa `alternarMarcaDoPaciente`, `odontogramas`, `alternarMarca`, `conferirMarca` e as condições.

## 🧪 Como testar

1. Com um plano aprovado que tenha uma restauração no dente 16 (faces M e O) e um tratamento de canal no 26, inicie o atendimento de uma consulta do paciente.
2. Escolha os dois itens e clique em **Marcar como realizados**: o aviso diz que o odontograma foi atualizado.
3. Abra a aba **Odontograma** da ficha: o 16 tem restauração nas faces M e O, e o 26 aparece com tratamento de canal.
4. Numa face com cárie marcada, realize uma restauração nela: a cárie dá lugar à restauração. Marcas que já existiam no dente continuam.
5. Um procedimento sem condição resultante (profilaxia) não muda o odontograma.

## 📎 Documentação afetada

- [[ProcedimentosRealizados]]
- [[AtendimentoDaConsulta]]
- [[MarcarCondicoes]]
- [[CondicoesELegenda]]
- [[ProcedimentoRealizadoNoOdontograma]]
- [[2026]] (changelog)
