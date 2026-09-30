---
tipo: funcionalidade
camada: frontend
area: Procedimentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, procedimentos, catalogo]
---

# Catálogo padrão de procedimentos

## O que é

A tabela de 32 procedimentos comuns, em oito especialidades, que a clínica recebe na primeira abertura do
app, para ter preços a editar em vez de uma lista vazia. É demonstração (ADR-001): preços fictícios e códigos
inventados para a clínica. A [[Lista de procedimentos]] o mostra, e o [[CadastroDeProcedimento]] o edita.

## Onde está no código

- `src/modulos/procedimentos/catalogo.ts` — `CATALOGO` (a lista) e `ESPECIALIDADES` (as oito áreas, na
  ordem em que a lista e o filtro as mostram).
- `src/modulos/procedimentos/sementes.ts` — o `semeador`, achado por `src/dados/semeadores.ts`.
- `src/modulos/procedimentos/catalogo.test.ts` e `sementes.test.ts` — os testes.
- `src/dominio/procedimento.ts` — o tipo `Procedimento`, com `codigo` e `condicaoResultante` opcionais.
- Dados: coleção `procedimentos`, em `src/dados/colecoes.ts`.

## Comportamento

- **Oito especialidades, 32 procedimentos**: Prevenção (5), Dentística (3), Endodontia (4), Periodontia (4),
  Cirurgia (4), Prótese (5), Implantodontia (3) e Ortodontia (4).
- **Código próprio**: a sigla de três letras da especialidade e dois dígitos (`PRE-02`, `END-01`). Não é o da
  TUSS e não se repete no catálogo.
- **Id legível** (`proc-profilaxia`), estável: é o que um item de plano guarda em `procedimentoId`.
- **Preço em centavos inteiros e duração em minutos**, todos positivos. Todos entram ativos.
- **Dente e face**: `exigeDente` vale para o que se faz num dente (restauração, canal, exodontia, coroa,
  implante, radiografia periapical); `exigeFace` só para o que se marca numa face, e implica `exigeDente`.
- **Condição no odontograma** (`condicaoResultante`, um `id` de `CONDICOES`), só onde o ato muda a marca do
  dente:

  | Condição | Procedimentos | Exige |
  |---|---|---|
  | `restauracao` | `DEN-01`, `DEN-02` | dente e face |
  | `selante` | `PRE-04` | dente e face |
  | `ausente` | `CIR-01`, `CIR-02`, `CIR-03` (exodontias) | dente |
  | `tratamentoDeCanal` | `END-01` a `END-04` | dente |
  | `coroa` | `PRO-01`, `PRO-02` | dente |
  | `implante` | `IMP-01` | dente |

  Enxerto ósseo, núcleo de preenchimento e coroa sobre implante não trazem condição: o dente já consta como
  implante, e os outros não mudam o desenho.
- **A semente** (`semeador`, chave `procedimentos`, versão 1) planta o catálogo só se a coleção
  `procedimentos` está vazia, e roda uma vez por versão. Depois da primeira carga ela não repõe nada, nem se a
  lista ficar vazia; subir a versão a faz rodar de novo, ainda só numa coleção vazia.

## Limites conhecidos

- `especialidade` segue texto livre no tipo: `ESPECIALIDADES` é a lista do catálogo padrão, que o filtro
  (item 6.2) e o cadastro (item 6.3) usam.
- A tabela é única: não há preço por convênio nem código de tabela oficial.
- O catálogo só nomeia atos que a clínica pode cobrar. O app não sugere conduta: nenhuma regra decide o que
  fazer em qual dente.

## Histórico de mudanças

- [[2026-09-30-pr-074-catalogo-padrao-de-procedimentos]] — O catálogo padrão de 32 procedimentos e a semente que o planta.
- [[2026-09-30-pr-096-cadastro-de-procedimento]] — os procedimentos do catálogo passam a poder ser editados, e novos, cadastrados.
- [[2026-09-30-pr-097-tratamentos-itens]] — o formulário do item do plano oferece só os procedimentos ativos, agrupados por especialidade.
