---
tipo: adr
numero: 1
data: 2026-09-30
status: aceito
tags: [adr, arquitetura, dados, lgpd]
---

# ADR-001 — Front-end primeiro, com dados locais atrás de uma fronteira

## Contexto

O Arcada guarda **dado de saúde de paciente** — anamnese, odontograma, periodontograma, evolução clínica —
além de CPF, telefone e o que cada um pagou. Dado referente à saúde é dado pessoal **sensível**
(Lei 13.709/2018, a LGPD, art. 5º, II). Guardá-lo num servidor exige, no mínimo, autenticação e controle
de acesso: decisões que a v1 não precisa tomar para validar o que importa agora — as telas, os fluxos e
as regras do consultório (dente, dinheiro, agenda). Ver [[visao-de-produto]].

Ao mesmo tempo, o produto só se testa de ponta a ponta se tiver estado persistente: cadastro, ficha,
odontograma, agenda, orçamento e caixa pressupõem que o dado sobreviva ao recarregar a página.

## Decisão

1. **A v1 é só front-end**: React + Vite, sem backend, sem banco e sem login. Roda no navegador e a demo é
   publicada como site estático.
2. **Os dados vivem no `localStorage` do navegador**, sempre por trás de **`src/dados/`** — tipos,
   coleções, hooks de leitura e funções de escrita. Só `src/dados/` toca `localStorage`; a única exceção é
   o kit visual, que guarda o tema e o colapso da coluna lateral ([[ADR-002-sistema-visual-vidro-organico]]).
3. **A tela nunca lê nem escreve `localStorage` direto.** Lê pelos hooks e escreve pelas funções de
   `src/dados/`, que **validam** o que recebem: a validação da tela é conforto; a de `src/dados/` é a regra.
4. **`src/dados/` é a fronteira que deixa uma API entrar depois.** Trocar o armazenamento por um backend
   mexe atrás dela, e as telas não mudam.
5. **A v1 é demonstração, não prontuário.**
   - Todo dado é fictício; os exemplos estáveis são `Paciente Exemplo` e `Dra. Exemplo`.
   - **Nenhum CPF em semente.** Todo CPF com dígito verificador válido pode ser de uma pessoa real; o
     teste da validação calcula o número no próprio teste.
   - Não há assinatura digital nem guarda legal de registro: o app não emite receita ou atestado com
     validade jurídica (o impresso sai com linha para assinatura à mão) e não sugere conduta clínica.

## Consequências

**A favor**

- Sem servidor, nenhum dado de paciente sai do navegador, e a demo é um site estático.
- Cada PR de módulo é pequeno e independente: nenhum precisa de migração, credencial ou ambiente
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- Trocar o armazenamento por uma API mexe em `src/dados/`, não nas telas — desde que as telas respeitem a
  fronteira, o que é regra dura do repositório de código.

**Custos**

- **Dado preso ao navegador.** Limpar os dados do site apaga tudo, e o que está no celular não aparece no
  computador. O mitigador é o backup — exportar e importar, no módulo 14 (Sistema).
- **Sem login, sem papéis, sem trilha de auditoria.** Quem abre o app vê tudo. Por isso o app **não pode ser
  usado com dado real de paciente**; uso real pede backend, login e controle de acesso por papel
  ([[roadmap]], depois da v1).
- **O `localStorage` tem cota pequena** (alguns MB por origem, conforme o navegador). Se o volume de
  odontogramas e exames chegar lá, o lugar da troca é o mesmo: atrás de `src/dados/`.
- **O sigilo vira contrato**: nunca dado real de pessoa em código, semente, teste, commit, print ou nota —
  o histórico do Git é permanente.

Quando entrar backend, esta ADR é substituída por uma nova (status `substituído`) que descreva
autenticação, base legal e armazenamento.

## Implementado em

- O repositório local — `criarColecao` (coleções tipadas sobre o `localStorage`, com versão de esquema), o
  hook `useColecao` e `novoId`, tudo em `src/dados/`, a fronteira que a decisão pede:
  [[2026-09-30-pr-024-repositorio-local]]; comportamento em [[RepositorioLocal]].
- As oito coleções do núcleo (`src/dados/colecoes.ts`) e os dados de demonstração fictícios — `Paciente Exemplo`,
  `Dra. Exemplo`, nenhum CPF —, plantados uma vez por versão e só em coleção vazia (`src/dados/sementes.ts`,
  chamada em `src/main.tsx` antes do primeiro render): [[2026-09-30-pr-035-colecoes-e-sementes]].
