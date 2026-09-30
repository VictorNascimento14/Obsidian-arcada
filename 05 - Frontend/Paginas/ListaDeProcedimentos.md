---
tipo: funcionalidade
camada: frontend
area: Procedimentos
rota: /procedimentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, procedimentos, lista, busca]
---

# Lista de procedimentos

## O que é

A tela de entrada do módulo Procedimentos: a tabela de procedimentos da clínica, com busca por nome e código
e filtro por especialidade. É onde a clínica confere o que oferece e por quanto. Lê o que a semente plantou
([[CatalogoPadrao]]) e o que a clínica cadastrar.

## Onde está no código

- `src/modulos/procedimentos/modulo.ts` — a rota `/procedimentos` e o item `Procedimentos` da coluna (grupo
  Cadastros, ordem 10, ícone `list`).
- `src/modulos/procedimentos/ListaProcedimentos.tsx` — a tela, com a seleção das linhas, o botão **Reajustar
  preços** e a marca **Inativo**.
- `src/modulos/procedimentos/ReajusteDePrecos.tsx` e `reajuste.ts` — o modal e a regra do reajuste em lote
  ([[ReajusteDePrecos]]).
- `src/modulos/procedimentos/busca.ts` — `filtrarProcedimentos` (busca, filtro e ordem) e
  `especialidadesDe` (as opções do filtro).
- Lê a coleção `procedimentos` (`src/dados/colecoes.ts`) por `useColecao`.

## Comportamento

- **Ordem**: por especialidade, na ordem do catálogo padrão (Prevenção, Dentística, Endodontia,
  Periodontia, Cirurgia, Prótese, Implantodontia, Ortodontia; as de fora dele vêm depois, em ordem
  alfabética) e, dentro de cada uma, por nome (pt-BR).
- **Busca**: cada palavra digitada tem de aparecer no nome ou no código, em qualquer ordem e sem distinguir
  acento nem caixa — `restauracao resina` acha `Restauração em resina composta`, e `cir-01` acha pelo
  código. A especialidade não entra na busca: para isso há o filtro.
- **Filtro por especialidade**: `Todas` e só as especialidades que a tabela tem hoje. Combina com a busca.
  Se a escolhida deixa de existir (o último procedimento dela mudou de área), o filtro volta a `Todas`.
- **Contagem**: `32 procedimentos`; com busca ou filtro, `5 de 32 procedimentos`. É uma região
  `role="status"`: o leitor de tela anuncia a cada letra digitada.
- **Linha**: o nome; `código · especialidade · exigência`; e, à direita, o preço em reais
  (`formatarReais`, guardado em centavos) e a duração em minutos. A exigência só aparece quando há: `Exige
  dente` ou `Exige dente e face`. Procedimento sem código omite o código.
- **Estados vazios**: sem nenhum procedimento, `Nenhum procedimento cadastrado ainda`; com filtro sem
  resultado, `Nenhum procedimento encontrado` e o botão `Limpar filtros`, que zera a busca e a especialidade.
- **Sem paginação**: a lista inteira vai para a tela; o catálogo tem dezenas de linhas.
- **Cadastro e edição**: o botão **Novo procedimento**, ao lado da busca, e o **Editar** de cada linha abrem o
  modal de [[CadastroDeProcedimento]].
- **Reajuste em lote**: cada linha tem uma caixa de seleção, e **Selecionar todos** marca a lista inteira (com
  busca ou filtro ativo, vira **Selecionar os visíveis**). O botão **Reajustar preços** abre o modal de
  [[ReajusteDePrecos]], que mostra a prévia antes de gravar; a contagem soma `· N selecionados`.
- **Ativo e inativo**: o procedimento inativo continua na lista, na busca e no filtro, com a marca **Inativo**
  ao lado do nome. Desativa-se e reativa-se pelo **Editar**, na caixa «Procedimento ativo»
  ([[AtivarEDesativarProcedimento]]).

## Movimento e micro-interações

A tela não tem movimento próprio: usa só a entrada da página do `PageShell`. O filtro é um `<select>`
nativo: no Chrome e no Edge a lista desdobra com o vidro do kit; nos outros navegadores aparece a lista do
sistema.

## Limites conhecidos

- Sem verificação visual em navegador (o Chrome de teste não conecta nesta máquina): conferi só que as
  classes novas existem no CSS do build.
- A linha em si não leva a lugar nenhum: a edição abre pelo botão **Editar** dela.

## Histórico de mudanças

- [[2026-09-30-pr-081-lista-de-procedimentos]] — A tela, a busca, o filtro por especialidade e o item na coluna.
- [[2026-09-30-pr-096-cadastro-de-procedimento]] — o botão **Novo procedimento** e o **Editar** de cada linha.
- [[2026-09-30-pr-121-reajuste-de-precos-em-lote]] — a seleção das linhas e o botão **Reajustar preços**.
- [[2026-09-30-pr-129-ativar-e-desativar-procedimento]] — a marca **Inativo** na lista.
