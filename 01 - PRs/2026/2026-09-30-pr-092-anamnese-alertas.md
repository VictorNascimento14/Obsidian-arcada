---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 92
url: https://github.com/VictorNascimento14/Arcada/pull/92
branch: feat/anamnese-alertas
tags: [pr, anamnese, alertas]
status: aberto
---

# PR #92 — feat(anamnese): derivar os alertas das respostas da anamnese

## 🎯 Contexto

Item 2.3 (Alertas derivados das respostas) do [[2026-09-30-plano-da-v1]]. Depende do tipo `Respostas` do item 2.1 (PR #82, já na `main`). Puro, sem tela e sem `modulo.ts`: o selo (`SeloAlertas`) vem no item 2.4. Fecha a issue #90.

## 🔧 Mudanças

- `src/modulos/anamnese/alertas.ts` (+ teste): `alertasDaAnamnese(respostas)`, que devolve `string[]`, e a tabela `REGRAS` (pergunta, texto e se o detalhe entra).

## 🕵️ Dado pessoal (LGPD)

Os alertas derivam de dado de saúde (alergia, gestação, diabetes), sensível pela LGPD. A função é pura: não guarda nem envia nada, só devolve texto a partir das respostas que já estão na ficha do paciente. Os exemplos dos testes são genéricos (“Penicilina”, “Látex”, “Arritmia”).

## 🧠 Decisões técnicas

- **Só repete o que foi respondido.** Cada texto nomeia o que o paciente marcou (“Gestante”, “Diabetes informado”), sem verbo de conduta, dose ou restrição — a regra do domínio no `CLAUDE.md`. O teste compara o texto exato de cada alerta: acrescentar uma recomendação quebra o teste.
- **Sete alertas**: os que o plano exemplifica (alergia, anticoagulante, gestante, diabetes) e os que o questionário do 2.1 tem da mesma natureza (reação a anestesia, pressão alta, problema no coração). Hábitos e histórico odontológico não geram alerta: são respostas comuns, e um selo em quase todo paciente deixaria de chamar atenção. Este corte é decisão de produto deste PR; mudá-lo é acrescentar ou tirar uma linha de `REGRAS`.
- **O detalhe entra só onde completa o aviso.** Alergia, reação a anestesia, anticoagulante e problema no coração aceitam o detalhe (“Alergia informada: Penicilina”); gestação, diabetes e pressão alta não têm campo de detalhe no questionário. O detalhe é aparado, e em branco vira o aviso sem os dois-pontos; o de uma resposta “não” nunca entra.
- **Devolve `string[]`, na ordem de `REGRAS`** (alergias primeiro), sem um tipo `Alerta`: os avisos não têm gravidade nem id próprio, e há um por pergunta, então os textos não se repetem. O item 2.4 pode envolver a lista no que precisar.
- **Confia no tipo `Respostas`.** A conferência de dado vindo de fora é de `respostasValidas` (item 2.1), a ser usada por quem grava e lê as respostas; aqui não se valida de novo.

## ⚠️ Armadilhas e aprendizados

- Uma regra que aponta para uma pergunta de texto compila e nunca dispara: o id é válido, mas a resposta é `string`. Toda regra nova entra com o seu caso no teste.
- `typeof resposta !== "object"` é o que separa a sim/não da de texto e da pergunta sem resposta; `resposta.sim` direto não compila, porque a união de `Respostas[PerguntaId]` inclui `string` e `undefined`.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — sem resposta “sim” não há alerta; os sete alertas com o texto exato; o detalhe entra só quando informado (e aparado); o detalhe de um “não”, o texto livre, os hábitos e o histórico odontológico não geram alerta.
2. `npm run type-check` — `REGRAS` cita as perguntas por `PerguntaId`, então um id que o questionário não tem não compila.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[glossario]]
- [[2026]] (changelog)
