---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 71
url: https://github.com/VictorNascimento14/Arcada/pull/71
branch: feat/pacientes-cadastro
tags: [pr, pacientes, cadastro, formulario]
status: merged
---

# PR #71 — feat(pacientes): cadastrar paciente com nome, nascimento e CPF validado

## 🎯 Contexto

Item 1.3 do [[2026-09-30-plano-da-v1]] (módulo 1 · Pacientes): o cadastro. Usa a máscara e a validação de CPF do item 1.2 e o `linkTelefone` do item 1.7 como critério de telefone válido; acrescenta a rota ao `modulo.ts` do item 1.1. Fecha a issue #57.

## 🔧 Mudanças

- `src/modulos/pacientes/regras.ts` e `regras.test.ts` — `validarPaciente` (erros por campo), `montarPaciente` (como o dado é guardado) e `cadastrarPaciente` (valida, monta e grava; lança sem gravar se inválido).
- `src/modulos/pacientes/FormularioPaciente.tsx` — os campos, a máscara do CPF, as mensagens depois da primeira tentativa de envio e o foco no primeiro campo com erro; `Button type="submit"` explícito.
- `src/modulos/pacientes/CadastroPaciente.tsx` e `CadastroPaciente.test.tsx` — a tela `/pacientes/novo` e os testes pela rota de verdade.
- `src/modulos/pacientes/modulo.ts` — a rota `/pacientes/novo`.
- `src/modulos/pacientes/ListaPacientes.tsx` — o botão `Novo paciente` ao lado da busca (e o teste dele).
- `src/dominio/paciente.ts` — `Paciente` ganha `observacoes?: string`.

## 🕵️ Dado pessoal (LGPD)

O formulário coleta dado pessoal: nome, nascimento, CPF, telefone, e-mail e uma observação livre. Tudo fica só no navegador (coleção local, ADR-001). O CPF é guardado só com os dígitos (`cpf.ts`) e não aparece em semente nem em teste escrito à mão: o teste calcula os verificadores. Os campos têm `autoComplete="off"` para o navegador não preencher o paciente com os dados de quem está usando o app, e as mensagens de erro não repetem o valor digitado.

## 🧠 Decisões técnicas

- **A regra é `regras.ts`; a tela só a reaproveita.** `FormularioPaciente` chama `validarPaciente` para escrever as mensagens; `cadastrarPaciente` chama a mesma função de novo antes de gravar e lança se falhar — o erro cita os campos, nunca os valores.
- **Mensagens só depois da primeira tentativa de envio**, e daí em diante recalculadas a cada tecla: formulário recém-aberto não mostra erro, e corrigir um campo tira a mensagem dele na hora. O primeiro campo inválido recebe o foco, e um `role="alert"` só para leitor de tela lê todos os erros de uma vez (o `TextField` do kit não liga a mensagem ao campo por `aria-describedby`).
- **`noValidate` e `autoComplete="off"`.** A validação nativa do navegador daria mensagens em outra língua e diferentes por navegador; o autopreenchimento ofereceria o nome, o telefone e o e-mail de quem usa o app para o paciente.
- **Telefone válido é o que `linkTelefone` aceita** (DDD + 8 ou 9 dígitos): todo telefone gravado dá botão de WhatsApp e de ligação na ficha. Em branco pode. Telefone estrangeiro não cabe — decisão a rever se a clínica atender fora do Brasil.
- **Nascimento** precisa existir no calendário (30/02 é recusado; 29/02 só em ano bissexto), não pode ser futuro (nascer hoje pode) e não pode ser anterior a 1900 (erro de digitação como `0202`).
- **Convênio em branco vira `Particular` ao gravar**, e campo opcional vazio (CPF, e-mail, observações) fica ausente no objeto, não como texto vazio.
- **Sem checagem de CPF repetido**: o item pede máscara e validação; achar duplicado é outra regra e fica de fora.

## ⚠️ Armadilhas e aprendizados

- O CPF de teste nunca é escrito à mão: `regras.test.ts` calcula os dois verificadores a partir de nove dígitos, e o teste de tela usa `11111111111`, que a regra recusa por serem todos iguais.
- DDD `00` não passa na regra de telefone: `contato.ts` tira os zeros à frente do número (o `0` de `(011)`), então `(00) 90000-0001` vira 9 dígitos. Os telefones da semente têm DDD `00` de propósito e, por isso, a ficha não mostrará os botões de contato para eles; os testes deste PR usam DDD `11` com números de exemplo.
- O `TextField` põe a mensagem de erro num `<p>` sem id; por isso o `role="alert"` repete o texto para o leitor de tela e o teste procura a mensagem fora dele (`noCampo`).

## 🧪 Como testar

1. `npm run dev`, abra `/pacientes` e toque em `Novo paciente`: o formulário abre sem nenhum erro à mostra.
2. Toque em `Cadastrar paciente` sem preencher: aparecem `Informe o nome do paciente.` e `Informe a data de nascimento.`, e o foco vai para o nome. Preencha o nome: a mensagem dele some na hora.
3. Digite `12345678` no CPF: o campo mostra `123.456.78`. Digite onze dígitos iguais (`11111111111`) e envie: aparece `CPF inválido. Confira os 11 dígitos.`
4. Preencha nome e nascimento (o calendário não deixa escolher data futura), deixe o convênio em branco e envie: o app vai para `/pacientes/<id>` (página não encontrada até o item 1.5) e, voltando à lista, o paciente novo aparece como `Particular`.
5. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam.

## 📎 Documentação afetada

- [[CadastroDePaciente]]
- [[ListaDePacientes]]
- [[CpfDoPaciente]]
- [[ContatoRapido]]
- [[TiposDoDominio]]
- [[2026]] (changelog)
