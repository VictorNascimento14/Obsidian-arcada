---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 64
url: https://github.com/VictorNascimento14/Arcada/pull/64
branch: feat/clinica-profissionais
tags: [pr, clinica, profissionais, cro]
status: aberto
---

# PR #64 — feat(clinica): cadastrar profissionais com CRO e cor na agenda

## 🎯 Contexto

Item 5.2 (Profissionais: CRO, cor na agenda) do [[2026-09-30-plano-da-v1]], sobre a tela `/clinica` aberta pelo PR #48. A regra do CRO (`croValido`, `formatarCro`) está na main desde o PR #38; o tipo `Profissional` só tinha nome, CRO e cor. Fecha a issue #54.

## 🔧 Mudanças

- `src/modulos/clinica/profissionais.ts` (+ teste): `CORES_DA_AGENDA` (8 cores), `validarProfissional`, `salvarProfissional` e `camposDoProfissional`.
- `src/modulos/clinica/Profissionais.tsx` (+ teste): o cartão com a lista e o modal de cadastro e edição (`Modal` do kit, radios para a cor).
- `src/modulos/clinica/PaginaClinica.tsx`: monta o cartão depois dos dados da clínica.
- `src/dominio/clinica.ts` (+ teste): `Profissional` ganha `especialidade` e `ativo`, opcionais, e a regra `profissionalAtivo`.

## 🧠 Decisões técnicas

- **`ativo` é opcional e a ausência conta como ativo.** As sementes e o que já está gravado no navegador não têm o campo, e torná-lo obrigatório pediria migração da coleção (arquivo compartilhado). A regra é uma só, `profissionalAtivo` em `src/dominio/clinica.ts`: quem lê a agenda deve usá-la em vez de `p.ativo`, porque `if (p.ativo)` esconderia todo mundo que veio da semente.
- **Cor: lista fechada de 8 cores CSS em texto** (`CORES_DA_AGENDA`), aplicada por `style`. `Profissional.cor` continua sendo CSS e nenhuma classe é montada em runtime. Todas dão contraste AA (4,5:1 ou mais) com texto branco por cima, e as duas cores das sementes (`#1f6f5b` e `#4a6fa5`) estão na lista.
- **Validar e gravar na mesma função** (`salvarProfissional`), como nos dados da clínica: devolve os erros por campo e a tela só os mostra. O CRO digitado solto passa por `formatarCro` e é gravado como `CRO-SP 00000`.
- **Só o formato do CRO é conferido**, não a repetição: dois profissionais com o mesmo registro são aceitos. Se doer, a checagem entra em `validarProfissional`.
- **Especialidade é texto livre** (até 60 caracteres), como `Procedimento.especialidade`.
- **Sem exclusão.** Ativo/inativo faz esse papel, para o histórico não apontar para um profissional que sumiu.
- **O cadastro é o `Modal` do kit, montado só enquanto aberto:** cada abertura começa com o formulário do zero.

## ⚠️ Armadilhas e aprendizados

- O botão **Salvar** fica no rodapé do modal, fora do `<form>`: liga por `form={id}` (id de `useId`). Sem o atributo, o clique não envia.
- Os radios de cor são `sr-only` e a seleção aparece como `outline` no irmão (`peer-checked`), que acompanha o raio da bolinha e deixa o vão transparente no vidro claro e no escuro. Sem verificação visual (o navegador de teste não conecta nesta máquina): conferido só que as classes existem no CSS do build.

## 🧪 Como testar

1. `npm run dev`, abra `/clinica` (item **Clínica** da coluna): o cartão «Profissionais» lista os dois da demonstração, com o CRO e a cor de cada um.
2. **Novo profissional**: preencha o nome e o CRO (`cro sp 123` vira `CRO-SP 123`), escolha uma cor e salve: ele aparece na lista com a cor escolhida.
3. Digite `12345` no CRO e salve: o campo mostra o erro, o modal continua aberto e nada é gravado.
4. **Editar** um profissional, desmarque «Profissional ativo» e salve: ele continua na lista, agora com «Inativo», depois dos ativos.
5. `npx vitest run --maxWorkers=2 src/modulos/clinica src/dominio` — regra, tela e a regra de ativo.

## 📎 Documentação afetada

- [[Profissionais]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
