---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 127
url: https://github.com/VictorNascimento14/Arcada/pull/127
branch: feat/financeiro-parcelas
tags: [pr, financeiro, lancamento, parcela, orcamento]
status: aberto
---

# PR #127 — feat(financeiro): gerar as parcelas do orçamento aprovado

## 🎯 Contexto

Item 10.1 do [[2026-09-30-plano-da-v1]] (módulo 10 · Financeiro): a geração das parcelas do orçamento aprovado. Usa `parcelar` (7.3, #55) e `total` (7.1, #49) do módulo de tratamentos, sem editá-lo, e grava na coleção do núcleo `lancamentos` (tipo `Lancamento`, #25 e #35). Fecha a issue #114.

## 🔧 Mudanças

- `src/modulos/financeiro/aParcelar.ts` — `SITUACOES_QUE_PARCELAM` e `planosParaParcelar`: aprovados ou em andamento, sem nenhum lançamento, por nome do paciente. Com teste.
- `src/modulos/financeiro/lancamentos.ts` — `calcularParcelas` (valida e divide, sem gravar) e `gerarParcelas` (grava um lançamento por parcela, de uma vez). Com teste.
- `PaginaFinanceiro.tsx`, `PlanosSemParcelas.tsx` e `FormularioDasParcelas.tsx` — a tela `/financeiro`, a lista e o modal com a prévia das parcelas. Teste de tela em `PaginaFinanceiro.test.tsx`.
- `AbaFinanceiro.tsx` — a aba da ficha, com os lançamentos do paciente por vencimento. Teste de tela em `AbaFinanceiro.test.tsx`.
- `src/modulos/financeiro/modulo.ts` — a rota `/financeiro`, o item da coluna (grupo `gestao`, ordem 20, ícone `coin`, `barraCelular`) e a aba (ordem 50); `modulo.test.ts` confere no registro.

## 🕵️ Dado pessoal (LGPD)

O financeiro liga valor e vencimento a um paciente pelo `pacienteId`. Os dados moram só no navegador (v1, [[ADR-001-frontend-primeiro-com-dados-locais]]); os testes usam pacientes de exemplo, sem CPF.

## 🧠 Decisões técnicas

- **A geração mora no financeiro**, não no botão Aprovar do plano: o módulo de tratamentos não é editado ([[ADR-003-modulos-por-pasta-com-registro-automatico]]) e o número de parcelas e o 1º vencimento pedem um formulário próprio. O plano aprovado espera na lista até ganhar as parcelas.
- **Um plano gera as parcelas uma vez.** `gerarParcelas` lança se o plano já tem lançamentos, e o plano parcelado sai da lista. A v1 não tem como apagar lançamentos (o estorno do 10.8 desfaz só a baixa), então parcela em dobro seria irreparável.
- **O modal mostra as parcelas antes de gerar**, com a mesma conta da gravação (`calcularParcelas`): pelo mesmo motivo, a geração não tem volta.
- **Gravação única.** As parcelas entram por `substituirTudo`, junto com os lançamentos que já existem: uma escrita e um aviso às telas, e entram todas ou nenhuma.
- **Teto de 60 parcelas** (`MAXIMO_DE_PARCELAS`) e parcela mínima de R$ 0,01. O `parcelar` deixa as últimas em `0` quando o total é menor que `n` e diz que quem parcela decide: aqui não se aceita.
- **O 1º vencimento não precisa ser futuro**: a regra só pede um dia que exista. O padrão do campo é hoje (`diaISO`).
- **Só importa, nunca edita**: `parcelar`, `total`, `SituacaoBadge` e `rotuloItens` vêm de tratamentos, e `dataBR`, de pacientes.

## ⚠️ Armadilhas e aprendizados

- **`parcelar` lança `RangeError` para vencimento inválido**, e o `<input type="date">` devolve `""` quando o campo está vazio ou incompleto. `calcularParcelas` confere o número e o total antes e captura o erro do vencimento para mostrá-lo no campo; sem isso a tela cairia.
- **Cada linha tem o seu botão** `Gerar parcelas`, então o nome acessível leva o paciente (`Gerar parcelas de <nome>`); é também o que os testes usam para achar o botão.
- **O `textContent` de um valor em reais traz o espaço não separável do `Intl`**: nos testes, normalize antes de comparar ([[2026-09-30-intl-separa-o-real-com-espaco-nao-separavel]]).
- **Não houve verificação visual no navegador** (o Chrome não conecta nesta máquina): o modal, o campo de data e a linha em telas estreitas foram conferidos só por teste de tela e pelo build.

## 🧪 Como testar

1. `npm run dev`: na aba **Tratamentos** da ficha de um paciente, crie um plano, adicione itens e aprove-o.
2. Abra **Financeiro** na coluna lateral: o plano aprovado aparece em `Planos sem parcelas`, com o paciente e o total.
3. Clique em **Gerar parcelas**, escolha 3 parcelas e o 1º vencimento em 31/01/2027: a prévia mostra 31/01, 28/02 e 31/03, e os valores somam o total (o resto dos centavos vai para as primeiras).
4. Confirme: o plano sai da lista e a aba **Financeiro** da ficha mostra as três parcelas por vencimento, `Em aberto`. Com o número em 0 ou o vencimento vazio, o campo mostra o erro e nada é gravado.
5. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[PlanosSemParcelas]]
- [[GeracaoDeParcelas]]
- [[ParcelamentoDoOrcamento]]
- [[FichaDoPaciente]]
- [[2026]] (changelog)
