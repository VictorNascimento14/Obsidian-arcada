---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 48
url: https://github.com/VictorNascimento14/Arcada/pull/48
branch: feat/clinica-dados
tags: [pr, clinica, cadastro]
status: merged
---

# PR #48 — feat(clinica): cadastrar os dados da clínica

## 🎯 Contexto

Item 5.1 (Dados da clínica) do [[2026-09-30-plano-da-v1]]. O tipo `Clinica` (`src/dominio/clinica.ts`) só tinha `nome` e `expediente`; agora tem também telefone, endereço, cidade e UF, todos opcionais. Fecha a issue #39.

## 🔧 Mudanças

- `src/modulos/clinica/modulo.ts`: rota `/clinica` e item da coluna (grupo `cadastros`, ordem 20, ícone `building`).
- `src/modulos/clinica/PaginaClinica.tsx`: a tela, dentro do `PageShell`; um cartão por assunto da clínica, empilhados.
- `src/modulos/clinica/DadosDaClinica.tsx`: o formulário (nome, telefone, endereço, cidade e UF num `<select>` das 27 siglas), com erro por campo e aviso ao salvar.
- `src/modulos/clinica/dadosDaClinica.ts` (+ teste): `validarDadosDaClinica`, `salvarDadosDaClinica`, `camposDaClinica` e os `LIMITES` de tamanho.
- `src/dominio/clinica.ts`: `Clinica` ganha `telefone`, `endereco`, `cidade` e `uf`, opcionais.
- Testes da regra, do formulário e da rota (o item da coluna abre `/clinica`).

## 🧠 Decisões técnicas

- **Validar e gravar na mesma função.** `salvarDadosDaClinica` valida e só então grava, devolvendo os erros por campo; a tela só mostra o que ela devolve (a validação da tela é conforto, a da função é a regra). Ela mora no módulo, e não em `src/dados/`, porque importa as siglas de `cro.ts` e `src/dados/` não importa de módulo.
- **Só o nome é obrigatório.** Telefone, endereço, cidade e UF são opcionais e ficam ausentes (não `""`) quando vazios. Limites: nome 100, telefone 20, endereço 150 e cidade 60 caracteres.
- **Telefone é texto livre**, sem máscara nem checagem de DDD: a clínica pode querer um 0800 ou um número sem DDD no cabeçalho.
- **UF é um `<select>`** das 27 siglas de `UFS` (`cro.ts`), o mesmo conjunto do registro no CRO; a regra recusa sigla fora da lista ou em minúsculas.
- **Salvar não toca o `expediente`.** Grava o registro atual com os cinco campos trocados; se a coleção estiver vazia, cria o registro com a semana fechada.
- **Sem semente nova.** A clínica de demonstração continua só com nome e expediente; o formulário abre com os demais campos vazios.

## ⚠️ Armadilhas e aprendizados

- O formulário lê a clínica só ao montar (estado inicial): uma gravação vinda de outra aba não sobrescreve o que a pessoa está digitando.
- O kit ainda não tem campo de seleção: o `<select>` de UF repete as classes do `TextField`. Sem verificação visual (o navegador de teste não conecta nesta máquina): conferido só que as classes literais existem no CSS do build.

## 🧪 Como testar

1. `npm run dev`, abra `http://localhost:3000` e clique em **Clínica** (grupo Cadastros) na coluna: abre `/clinica` com o cartão «Dados da clínica».
2. Preencha telefone, endereço e cidade, escolha uma UF e clique em **Salvar**: aparece o aviso «Dados da clínica salvos». Recarregue a página: os valores continuam lá.
3. Apague o nome e salve: o campo mostra «Informe o nome da clínica.», aparece «Revise os campos destacados.» e nada é gravado.
4. `npm test -- src/modulos/clinica` — regra, formulário e rota.

## 📎 Documentação afetada

- [[Clinica]]
- [[2026-09-30-plano-da-v1]]
- [[2026]] (changelog)
