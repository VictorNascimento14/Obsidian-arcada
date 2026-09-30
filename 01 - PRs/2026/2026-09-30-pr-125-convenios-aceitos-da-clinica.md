---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 125
url: https://github.com/VictorNascimento14/Arcada/pull/125
branch: feat/clinica-convenios
tags: [pr, clinica, convenios]
status: aberto
---

# PR #125 — feat(clinica): cadastrar os convênios aceitos pela clínica

## 🎯 Contexto

Item 5.5 (Convênios aceitos) do [[2026-09-30-plano-da-v1]], sobre a tela `/clinica` com os cartões de dados (PR #48), profissionais (PR #64), cadeiras (PR #69) e expediente (PR #107). O tipo `Clinica` não tinha onde guardar a lista. Fecha a issue #120.

## 🔧 Mudanças

- `src/dominio/clinica.ts`: `Clinica` ganha `convenios`, opcional.
- `src/modulos/clinica/convenios.ts` (+ teste): `validarConvenio`, `adicionarConvenio` e `removerConvenio`.
- `src/modulos/clinica/Convenios.tsx` (+ teste): o cartão com a lista, o campo de adicionar e o botão de remover.
- `src/modulos/clinica/PaginaClinica.tsx` (+ teste): monta o cartão depois das cadeiras.
- `src/modulos/clinica/dadosDaClinica.ts`: só exporta `SEMANA_FECHADA`, que a gravação usa para criar o registro quando ele não existe.

## 🧠 Decisões técnicas

- **A lista mora no registro da clínica** (`Clinica.convenios`), não numa coleção própria: é uma lista curta de nomes, sem `id` nem campo além do nome, e a clínica é um registro só. `salvarDadosDaClinica` e `salvarExpediente` já regravam o registro com `...atual`, então a lista sobrevive a eles.
- **`convenios` é opcional**: a semente e o que já está gravado no navegador não têm o campo, e a ausência conta como lista vazia.
- **Sem repetir, ignorando caixa, acento e espaço a mais.** A comparação é do `Intl.Collator` do pt-BR com `sensitivity: base`, então `Convênio Exemplo` e `convenio  exemplo` são o mesmo convênio. O nome é guardado aparado e com um espaço só entre as palavras, com a grafia de quem cadastrou.
- **Ordem alfabética na escrita.** `adicionarConvenio` ordena (pt-BR, `Ágil` junto do A) ao gravar; quem for consumir a lista já a recebe em ordem.
- **Só o nome, sem renomear** (comentário `ponytail:` em `convenios.ts`). Para trocar um nome, remove-se e acrescenta-se de novo. Tirar um convênio da lista não mexe nos pacientes: `Paciente.convenio` segue texto livre.
- **Remover não pede confirmação**: só tira o nome da lista de aceitos e se desfaz acrescentando-o de novo; nenhum dado de paciente ou de plano depende dela ainda.
- **Sem registro da clínica, cria um só com a lista** (nome vazio e semana fechada), como `salvarDadosDaClinica` já faz; o nome se preenche no cartão de dados.

## ⚠️ Armadilhas e aprendizados

- O cadastro do paciente e a tabela de preços ainda **não** leem esta lista: o cartão só a mantém. Quando esses módulos a consumirem, o `Paciente.convenio` (texto livre) e os convênios já cadastrados nos pacientes de demonstração (`Convênio Exemplo`, `Particular`) não estarão nela.
- `removerConvenio` compara o nome exato, como está guardado; a tela sempre o passa como veio da lista.
- Sem verificação visual (o navegador de teste não conecta nesta máquina): o cartão só usa classes escritas por extenso no fonte, que o JIT do Tailwind gera, e componentes do kit (`TextField`, `Button`); o `mt-[1.625rem]` do botão é a altura do rótulo do `TextField`.

## 🧪 Como testar

1. `npm run dev`, abra `/clinica` (item **Clínica** da coluna): o cartão «Convênios aceitos» diz «Nenhum convênio cadastrado ainda.» (a demonstração não traz convênios).
2. Digite um nome (por exemplo `Plano Alfa`) e clique em «Adicionar», ou aperte Enter: o nome entra na lista e o campo esvazia. Acrescente outro: a lista fica em ordem alfabética.
3. Tente adicionar o mesmo nome com outra caixa ou sem acento (`plano alfa`), ou o campo vazio: o campo mostra o erro, mantém o texto e nada é gravado.
4. Clique em «Remover» num nome: ele sai da lista, e os outros ficam. Recarregue a página: a lista continua como ficou, e os dados da clínica e o expediente seguem iguais.
5. `npx vitest run --maxWorkers=2 src/modulos/clinica` — regra, cartão e a tela.

## 📎 Documentação afetada

- [[Convenios]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
