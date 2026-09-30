---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 69
url: https://github.com/VictorNascimento14/Arcada/pull/69
branch: feat/clinica-cadeiras
tags: [pr, clinica, cadeiras]
status: aberto
---

# PR #69 — feat(clinica): cadastrar as cadeiras

## 🎯 Contexto

Item 5.3 (Cadeiras) do [[2026-09-30-plano-da-v1]], sobre a tela `/clinica` aberta pelo PR #48 e o cartão de profissionais do PR #64, cujo desenho (lista e modal, ativo/inativo por uma regra única no domínio) este PR repete. O tipo `Cadeira` só tinha `id` e `nome`. Fecha a issue #66.

## 🔧 Mudanças

- `src/modulos/clinica/cadeiras.ts` (+ teste): `validarCadeira`, `salvarCadeira` e `camposDaCadeira`.
- `src/modulos/clinica/Cadeiras.tsx` (+ teste): o cartão com a lista e o modal de cadastro e edição (`Modal` do kit).
- `src/modulos/clinica/PaginaClinica.tsx`: monta o cartão depois dos profissionais.
- `src/dominio/clinica.ts` (+ teste): `Cadeira` ganha `ativa`, opcional, e a regra `cadeiraAtiva`.

## 🧠 Decisões técnicas

- **`ativa` é opcional e a ausência conta como ativa**, como em `Profissional.ativo`: a semente e o que já está gravado no navegador não têm o campo. A regra única é `cadeiraAtiva`, em `src/dominio/clinica.ts`; quem lê a agenda deve usá-la em vez de `c.ativa`, que esconderia as cadeiras da semente.
- **Só o nome é obrigatório** (até 60 caracteres). Não há checagem de nome repetido; se duas cadeiras com o mesmo nome atrapalharem a agenda, a checagem entra em `validarCadeira`.
- **Lista: ativas primeiro, e cada grupo por nome com o número por valor** (`Cadeira 2` antes de `Cadeira 10`).
- **Sem exclusão.** Ativa/inativa faz esse papel, para o histórico não apontar para uma cadeira que sumiu.
- **Validar e gravar na mesma função** (`salvarCadeira`), como nos demais cadastros da clínica: devolve o erro e a tela só o mostra.
- **Sem componente genérico de lista e modal.** `Cadeiras.tsx` repete a estrutura de `Profissionais.tsx`: são só dois usos, com campos diferentes; extrair fica para o terceiro.

## ⚠️ Armadilhas e aprendizados

- O botão **Salvar** fica no rodapé do modal, fora do `<form>`: liga por `form={id}` (id de `useId`), o mesmo desenho do cartão de profissionais.
- Sem verificação visual (o navegador de teste não conecta nesta máquina): o cartão só usa classes que o de profissionais já usa e que estão no CSS do build.

## 🧪 Como testar

1. `npm run dev`, abra `/clinica` (item **Clínica** da coluna): o cartão «Cadeiras» lista «Cadeira 1» e «Cadeira 2», da demonstração.
2. **Nova cadeira**: digite um nome e salve: ela aparece na lista. Salve sem nome: o campo mostra o erro, o modal continua aberto e nada é gravado.
3. **Editar** uma cadeira, desmarque «Cadeira ativa» e salve: ela continua na lista, agora com «Inativa», depois das ativas.
4. `npx vitest run --maxWorkers=2 src/modulos/clinica src/dominio` — regra, tela e a regra de ativa.

## 📎 Documentação afetada

- [[Cadeiras]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
