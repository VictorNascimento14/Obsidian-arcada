---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 111
url: https://github.com/VictorNascimento14/Arcada/pull/111
branch: feat/anamnese-selo
tags: [pr, anamnese, alertas, selo]
status: aberto
---

# PR #111 — feat(anamnese): mostrar os alertas da anamnese em um selo para o cartão e a ficha

## 🎯 Contexto

Item 2.4 (Selo de alerta no cartão e na ficha) do [[2026-09-30-plano-da-v1]]. Usa os alertas do item 2.3 (PR #92) e a coleção `anamneses` do item 2.2 (PR #104). Pendência: o encaixe em `ListaPacientes` e `FichaPaciente`, que são do módulo de pacientes. Fecha a issue #105.

## 🔧 Mudanças

- `src/modulos/anamnese/SeloAlertas.tsx` (+ teste): o componente (exportação padrão), `<SeloAlertas pacienteId={id} />`.
- `src/modulos/anamnese/FormularioAnamnese.tsx` (+ teste): mostra o selo no topo da aba, abaixo da data da última versão.

## 🕵️ Dado pessoal (LGPD)

O selo mostra dado de saúde (alergia, gestação, medicamento). Onde for encaixado — o plano pede o cartão da lista —, esses alertas ficam visíveis a quem vê a lista de pacientes: é a decisão de produto do item 2.4, e vale conferir o encaixe com isso em mente. Nada é gravado nem enviado; o texto sai das respostas que já estão no `localStorage`, e os exemplos dos testes são genéricos.

## 🧠 Decisões técnicas

- **Uma pílula por alerta, com o texto completo.** O detalhe de alergia ou de medicamento não é cortado com reticências: é informação de segurança, então a pílula quebra de linha em vez de esconder (`rounded-2xl`, e não `rounded-full`, para a pílula de duas linhas não virar cápsula).
- **Só vale a versão mais recente.** O selo lê `versoesDoPaciente(...)[0]`; o alerta de uma versão antiga que foi corrigida depois não conta.
- **O selo só desenha se o registro passar em `respostasValidas`.** `alertasDaAnamnese` confia no tipo (PR #92) e lança se uma resposta sim/não guardada for `null`; o selo é quem lê dado guardado, então é ele que valida, e uma lista de cartões não cai por um registro estragado. O teto: se o questionário ganhar uma pergunta obrigatória, as versões antigas deixam de passar e o selo delas some. O caminho é subir a `versao` da coleção `anamneses` e migrar com `migrar` (`criarColecao`).
- **Selo ausente não é "sem alergia".** Quem nunca preencheu a anamnese também não tem selo: "sem anamnese" não é um alerta derivado de resposta, e a regra do domínio é alerta só repetir o que foi respondido.
- **Cor laranja do tema** (`orange-100` com `orange-800`): a rampa `accent` é verde, e a laranja acompanha o tema escuro. A cor ajuda; quem diz o alerta é o texto, e o ícone é `aria-hidden`.
- **Na aba, o selo mostra a última versão salva, não o rascunho.** Se seguisse o que está sendo digitado, mostraria um alerta que o cartão da lista ainda não mostra; o teste cobre isso.
- **O encaixe em pacientes fica com o orquestrador.** Não há slot no `Modulo`, e módulo não edita o de outro. Duas saídas: o cartão de `ListaPacientes` e o cabeçalho de `FichaPaciente` importam `SeloAlertas` de `@/modulos/anamnese/SeloAlertas` (abaixo do telefone); ou, no espírito da [[ADR-003-modulos-por-pasta-com-registro-automatico]], o `Modulo` ganha um campo `seloPaciente` que o `NAVEGACAO` reúne, e pacientes não importa anamnese.

## ⚠️ Armadilhas e aprendizados

- O contêiner do selo na aba tem `empty:hidden`: sem alerta o selo devolve `null`, e um `div` com margem ficaria como um vazio entre o texto e o formulário. Funciona porque o React não deixa nó de texto dentro do `div`.
- Cada cartão da lista chama `versoesDoPaciente` (filtra e ordena a coleção inteira): custa `pacientes × versões` por renderização. Para dezenas de pacientes e algumas centenas de versões é desprezível; com mais que isso, indexar por paciente.

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/anamnese` — o selo (versão mais recente, vazio, registro estragado, reatividade) e a aba com o selo no topo.
2. `npm run dev`, abrir um paciente e a aba **Anamnese**: responder tudo "Não", marcar "Sim" em Alergia (detalhe "Látex") e em Gestante e salvar. As pílulas "Alergia informada: Látex" e "Gestante" aparecem sob "Última versão".
3. Marcar outro "Sim" sem salvar: as pílulas não mudam. Salvar de novo com tudo "Não": as pílulas somem, e o espaço delas também.

## 📎 Documentação afetada

- [[2026-09-30-plano-da-v1]]
- [[SeloAlertas]]
- [[FormularioAnamnese]]
- [[Lista de pacientes]]
- [[Ficha do paciente]]
- [[2026-09-30-pr-092-anamnese-alertas]]
- [[glossario]]
- [[2026]] (changelog)
