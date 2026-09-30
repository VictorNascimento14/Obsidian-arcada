---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 119
url: https://github.com/VictorNascimento14/Arcada/pull/119
branch: feat/pacientes-alertas
tags: [pr, pacientes, anamnese]
status: aberto
---

# PR #119 — feat(pacientes): mostrar os alertas da anamnese no cartão e na ficha

## 🎯 Contexto

Integração pendente do item 2.4 do [[2026-09-30-plano-da-v1]]: o módulo de anamnese não edita o de pacientes, então o encaixe fica aqui. Fecha a issue #117.

## 🔧 Mudanças

- `src/modulos/pacientes/FichaPaciente.tsx` — selo abaixo do nome e telefone.
- `src/modulos/pacientes/ListaPacientes.tsx` — selo no cartão.
- `src/modulos/pacientes/alertas.test.tsx` — só o cartão de quem informou mostra o selo.

## 🧠 Decisões técnicas

- O módulo de pacientes importa o componente que o de anamnese exporta — importar é permitido; editar o outro módulo, não.
- Contêiner com `empty:hidden`: sem alerta, o `SeloAlertas` devolve nada e não sobra margem.

## 🧪 Como testar

1. Numa anamnese, responder "sim" em alergia com detalhe e salvar.
2. A lista de pacientes e o topo da ficha mostram "Alergia informada: …"; paciente sem alerta não ganha espaço vazio.
3. `npx vitest run src/modulos/pacientes/alertas.test.tsx`.

## 📎 Documentação afetada

- [[SeloAlertas]]
- [[FichaDoPaciente]]
- [[ListaDePacientes]]
- [[2026]] (changelog)
