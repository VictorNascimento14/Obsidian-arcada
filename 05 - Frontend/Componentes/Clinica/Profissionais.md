---
tipo: funcionalidade
camada: frontend
area: Clinica
rota: /clinica
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, clinica, profissionais, cro]
---

# Profissionais

## O que é

O cartão da equipe na tela [[Clinica]]: a lista de quem atende e o cadastro, com o registro no CRO, a
especialidade e a cor com que cada um aparece na agenda.

## Onde está no código

- `src/modulos/clinica/Profissionais.tsx` — o cartão e o modal de cadastro e edição.
- `src/modulos/clinica/profissionais.ts` — `CORES_DA_AGENDA`, `validarProfissional`,
  `salvarProfissional` e `camposDoProfissional`.
- `src/modulos/clinica/cro.ts` — `croValido` e `formatarCro`, que o cadastro usa.
- `src/dominio/clinica.ts` — o tipo `Profissional` e `profissionalAtivo`.
- Dados: coleção `profissionais`, em `src/dados/colecoes.ts`.

## Comportamento

- **Lista**: ativos primeiro, cada grupo por nome. Cada linha mostra a cor, o nome,
  `CRO · especialidade` e, quando for o caso, a marca «Inativo».
- **Novo profissional** e **Editar** abrem o mesmo modal. Campos: nome (obrigatório, até 100
  caracteres), registro no CRO (obrigatório), especialidade (opcional, até 60), cor na agenda e
  «Profissional ativo».
- **CRO**: só o formato. O que é digitado solto (`cro sp 123`, `CRO/SP 123`) é arrumado por
  `formatarCro` e gravado como `CRO-SP 123`; sem UF válida ou sem número, o campo mostra o erro e nada é
  gravado. Não confere se o registro existe no conselho nem se já está cadastrado.
- **Cor**: uma entre 8 (verde, azul, roxo, terracota, âmbar, rosa, turquesa e grafite), escolhida em
  bolinhas. É gravada como CSS em texto (`#1f6f5b`) e aplicada por `style`; um profissional novo começa
  na primeira.
- **Ativo e inativo**: não há exclusão. Inativo continua na lista e no histórico, mas some das escolhas.
  Quem lê a agenda deve usar `profissionalAtivo(p)`: o campo `ativo` pode faltar (dado da semente) e a
  ausência conta como ativo.
- **Salvar** valida pela função de escrita. Com erro, o modal continua aberto e cada campo mostra a sua
  mensagem. Cancelar, Esc e clique fora fecham sem gravar.

## Movimento e micro-interações

O modal é o do kit: o foco entra nele e volta ao botão que o abriu quando fecha. A cor escolhida ganha um
contorno, que acompanha também o foco do teclado nos radios.

## Histórico de mudanças

- [[2026-09-30-pr-064-profissionais-da-clinica]] — Lista e cadastro de profissionais, com CRO validado, cor na agenda e ativo/inativo.
