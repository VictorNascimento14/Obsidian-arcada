---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 178
url: https://github.com/VictorNascimento14/Arcada/pull/178
branch: feat/sistema-restaurar
tags: [pr, sistema, demonstracao, dados, sementes]
status: aberto
---

# PR #178 — feat(sistema): restaurar os dados de demonstração apagando o que o navegador guarda

## 🎯 Contexto

Item 14.5 (Restaurar dados de demonstração) do [[2026-09-30-plano-da-v1]], segundo PR do módulo **Sistema**, sobre a tela `/sistema` e o `src/dados/backup.ts` do item 14.1 ([[Sistema]], PR #171). Usa as sementes de `src/dados/sementes.ts` (`carregarSementes`: uma vez por versão, só em coleção vazia) e a camada de dados da [[ADR-001-frontend-primeiro-com-dados-locais]]. Fecha a issue #175.

## 🔧 Mudanças

- `src/dados/backup.ts` (+ teste): `restaurarDemonstracao()` apaga as chaves `arcada:*`, marcas de semente incluídas, e poupa o resto do `localStorage`.
- `src/modulos/sistema/CartaoDeRestauracao.tsx` (+ teste): o cartão e o modal de confirmação; ao confirmar, chama a regra e recarrega a página.
- `src/modulos/sistema/PaginaSistema.tsx` (+ teste): o cartão entra abaixo do backup.

## 🕵️ Dado pessoal (LGPD)

Apaga dado pessoal sensível (o consultório inteiro) sem volta: por isso o modal avisa que não dá para desfazer e sugere exportar um backup antes. Nada é enviado a lugar nenhum, e a v1 só tem dado fictício.

## 🧠 Decisões técnicas

- **A marca de semente é apagada junto** (`arcada:sementes:*`): `carregarSementes` pula o semeador cuja marca está na versão, então com a marca no lugar a página recarregaria vazia. É o que faz «as sementes plantarem de novo», e o teste prova com o `carregarSementes` de verdade. Tema e coluna usam traço, ficam fora do prefixo e não precisam de exceção.
- **Só o prefixo `arcada:` é apagado**, nunca o `localStorage` inteiro (`clear()`): a origem é compartilhada com outros apps e as preferências do kit precisam sobreviver.
- **A regra não recarrega; a tela recarrega.** `restaurarDemonstracao` só mexe nas chaves: as coleções guardam o estado em memória e o semeador só planta em coleção vazia, então é o `location.reload()` do cartão que leva a demonstração de volta.
- **Reuso**: a limpeza é a mesma troca de chaves do importador (`trocarChaves` com nada para gravar), sem varredura nova.
- **Este item não sabe o que as sementes contêm**: quem planta a demonstração continua sendo o `carregarSementes` do `main.tsx`.

## ⚠️ Armadilhas e aprendizados

- Um teste que só confere «as chaves sumiram» passaria mesmo com a marca de semente de fora e a página vazia depois do reload. O teste decisivo roda `carregarSementes` duas vezes antes e uma depois de restaurar, e conta as chamadas do semeador ([[2026-09-30-restaurar-a-demonstracao-apaga-tambem-a-marca-de-semente]]).
- O teste das preferências do kit grava o tema e o colapso pelas funções do kit (`definirTema`, `setSidebarCollapsed`) e compara as chaves que elas escreveram, sem repetir o nome de nenhuma. O `afterEach` devolve os dois ao padrão, porque `setSidebarCollapsed` guarda o estado em módulo e não escreve se o valor não muda.
- No teste de tela, `location` vira um objeto com `reload` dublê (`vi.stubGlobal`), como no cartão de backup.

## 🧪 Como testar

1. Abra `/sistema` e, no cartão **Restaurar a demonstração**, clique em **Restaurar demonstração**: abre «Apagar tudo e restaurar a demonstração?». **Cancelar** fecha sem mudar nada.
2. Cadastre um paciente em `/pacientes/novo`, volte a `/sistema`, clique de novo e confirme em **Apagar e restaurar**: a página recarrega e a lista de pacientes volta aos de exemplo (o novo some).
3. Antes de restaurar, troque o tema para escuro e recolha a coluna lateral: depois de restaurar, o tema e a coluna continuam como estavam.

## 📎 Documentação afetada

- [[RestauracaoDaDemonstracao]]
- [[Sistema]]
- [[2026-09-30-restaurar-a-demonstracao-apaga-tambem-a-marca-de-semente]]
- [[ADR-001-frontend-primeiro-com-dados-locais]]
- [[2026]] (changelog)
