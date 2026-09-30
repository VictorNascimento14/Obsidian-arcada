---
tipo: funcionalidade
camada: frontend
area: Clinica
rota: /clinica
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, clinica, convenios]
---

# Convênios aceitos

## O que é

O cartão dos convênios que a clínica atende, na tela [[Clinica]]: uma lista simples de nomes, com um campo para
acrescentar e um botão para remover cada um. É a lista que o cadastro do paciente e a tabela de preços vão usar;
por enquanto o cartão só a mantém.

## Onde está no código

- `src/modulos/clinica/Convenios.tsx` — o cartão.
- `src/modulos/clinica/convenios.ts` — `validarConvenio`, `adicionarConvenio` e `removerConvenio`.
- `src/dominio/clinica.ts` — o campo `convenios?: string[]` do tipo `Clinica`.
- Dados: o campo `convenios` do registro único da coleção `clinica` (`CLINICA_ID`), em `src/dados/colecoes.ts`.

## Comportamento

- **Lista**: os nomes em ordem alfabética (pt-BR), cada um com o botão «Remover». Sem nenhum, mostra «Nenhum
  convênio cadastrado ainda.» A demonstração não traz convênios.
- **Adicionar**: o campo «Nome do convênio» e o botão «Adicionar» (ou Enter). O nome é obrigatório, tem até 60
  caracteres e não pode repetir um que já está na lista — a comparação ignora caixa, acento e espaço a mais
  (`Convênio Exemplo` e `convenio  exemplo` são o mesmo). Com erro, o campo mostra a mensagem, mantém o texto e
  nada é gravado. Sem erro, grava, esvazia o campo e avisa «Convênio adicionado».
- **O que é gravado**: o nome aparado e com um espaço só entre as palavras, com a grafia de quem cadastrou, e a
  lista inteira reordenada. O resto do registro da clínica não é tocado.
- **Remover**: tira o nome da lista na hora, sem pedir confirmação, e avisa «Convênio removido». Não mexe em
  pacientes nem em planos: `Paciente.convenio` é texto livre e não depende da lista.
- **Sem renomear**: para trocar um nome, remove-se e acrescenta-se de novo.
- **Sem registro da clínica**, o primeiro convênio cria um só com a lista (nome vazio e semana fechada); o nome
  se preenche no cartão de dados.

## Movimento e micro-interações

O cartão entra subindo quando aparece (`GlassCard`); ao adicionar ou remover, um aviso passageiro confirma.

## Histórico de mudanças

- [[2026-09-30-pr-125-convenios-aceitos-da-clinica]] — o cartão de convênios aceitos, depois das cadeiras: lista simples de nomes no registro da clínica.
