---
tipo: adr
numero: 2
data: 2026-09-30
status: aceito
tags: [adr, design, frontend]
---

# ADR-002 — Sistema visual vidro-orgânico, copiado em `src/ui/`

## Contexto

O Arcada roda em celular, tablet e computador e precisa de uma linguagem visual coerente desde o primeiro
PR, sem que cada módulo invente a sua. Existe um sistema visual próprio, o **vidro-orgânico** (repositório
`VictorNascimento14/Design-moderno`): verde profundo sobre uma base quase branca esverdeada, cartões de
vidro fosco, pílulas e uma coluna lateral flutuante que desdobra. O kit traz a fundação (`index.css` e
`tailwind.config.ts`), os primitivos (`GlassCard`, `Button`, `TextField`, `Modal`, `Dropdown`,
`Calendar`…), a casca do app (`RailLayout`, `PageShell`, barra de baixo do celular) e as invariantes do
sistema — cada uma nasceu de um bug real.

Há dois jeitos de reaproveitar um kit: **depender dele** de fora (pacote, submódulo, import de outro
repositório) ou **copiá-lo**. O próprio kit foi feito para ser copiado: o instalador leva `kit/` para
`src/ui/` do projeto, e o kit só depende de `react`, `react-dom` e `react-router-dom`.

## Decisão

1. O sistema visual do Arcada é o kit vidro-orgânico, **copiado** em `src/ui/` e importado por `@/ui`. A
   fundação é `src/ui/index.css` mais `tailwind.config.ts`; os primitivos, `src/ui/base/`; a casca,
   `src/ui/shell/`; as utilidades, `src/ui/lib/` e `src/ui/hooks/`.
2. A relação é de **cópia num instante**: o Arcada não importa nada do repositório do kit — nem pacote, nem
   submódulo, nem link. A cópia é a do dia em que foi feita e evolui sozinha depois.
3. **`src/ui/` é fundação: não se edita para acertar uma tela.** A tela se resolve compondo os
   primitivos. Mudar uma rampa de cor repinta o app inteiro.
4. **Correção no kit volta por PR lá.** Defeito que é do kit — e não da tela — se corrige no repositório
   `Design-moderno`, por PR. Consertar só na cópia do Arcada deixa a correção presa aqui, e a próxima
   atualização do kit a desfaz.
5. As invariantes do sistema vivem na seção "Sistema visual" do `CLAUDE.md` do repositório de código; o
   vocabulário de movimento, em [[linguagem-visual]].

## Consequências

**A favor**

- O app nasce coerente: cada PR de módulo compõe primitivos prontos, e as invariantes já vêm escritas.
- Nenhuma dependência externa no build: o Arcada compila sozinho.
- A casca (coluna lateral, cabeçalho, barra de baixo do celular) já vem pronta e igual para todas as
  telas, o que deixa cada módulo só com o seu conteúdo.

**Custos**

- **A cópia envelhece.** Melhoria do kit não chega sozinha. Atualizar é rodar o instalador do kit de novo,
  que troca `src/ui/` inteiro (guardando `src/ui.bak`) e não faz merge: quem editou algo lá perde a
  edição se não comparar com a cópia de segurança.
- **Pressão para editar `src/ui/`** sempre que uma tela quase cabe no primitivo. A regra é dura: se o
  primitivo não serve, a tela compõe outra coisa, ou o kit ganha o recurso por PR lá.
- **O Arcada herda as escolhas do kit** — verde profundo, vidro fosco, movimento. Trocar a identidade
  visual é trocar a fundação, e isso pede uma ADR nova.

Relacionadas: [[ADR-001-frontend-primeiro-com-dados-locais]] (o kit é a exceção que guarda tema e colapso
da coluna no `localStorage`) e [[ADR-003-modulos-por-pasta-com-registro-automatico]] (a coluna lateral
recebe o item de cada módulo).

## Implementado em

TODO: PR que implementar (previsto em [[2026-09-30-plano-da-v1]]).
