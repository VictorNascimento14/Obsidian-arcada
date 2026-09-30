---
tipo: funcionalidade
camada: frontend
area: Clinica
rota: /clinica
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, clinica, cadeiras]
---

# Cadeiras

## O que é

O cartão dos postos de atendimento na tela [[Clinica]]: a lista das cadeiras e o cadastro. A agenda é
organizada por cadeira.

## Onde está no código

- `src/modulos/clinica/Cadeiras.tsx` — o cartão e o modal de cadastro e edição.
- `src/modulos/clinica/cadeiras.ts` — `validarCadeira`, `salvarCadeira` e `camposDaCadeira`.
- `src/dominio/clinica.ts` — o tipo `Cadeira` e `cadeiraAtiva`.
- Dados: coleção `cadeiras`, em `src/dados/colecoes.ts`.

## Comportamento

- **Lista**: ativas primeiro, cada grupo por nome, com o número por valor (`Cadeira 2` antes de
  `Cadeira 10`). Cada linha mostra o nome e, quando for o caso, a marca «Inativa».
- **Nova cadeira** e **Editar** abrem o mesmo modal. Campos: nome (obrigatório, até 60 caracteres) e
  «Cadeira ativa».
- **Ativa e inativa**: não há exclusão. Inativa continua na lista e no histórico, mas some das escolhas.
  Quem lê a agenda deve usar `cadeiraAtiva(c)`: o campo `ativa` pode faltar (dado da semente) e a
  ausência conta como ativa.
- **Nome repetido** é aceito: não há checagem.
- **Salvar** valida pela função de escrita. Sem nome, o modal continua aberto e o campo mostra o erro.
  Cancelar, Esc e clique fora fecham sem gravar.

## Movimento e micro-interações

O modal é o do kit: o foco entra nele e volta ao botão que o abriu quando fecha.

## Histórico de mudanças

- [[2026-09-30-pr-069-cadeiras-da-clinica]] — Lista e cadastro de cadeiras, com ativa/inativa.
