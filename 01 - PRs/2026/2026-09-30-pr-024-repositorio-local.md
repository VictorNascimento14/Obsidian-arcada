---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 24
url: https://github.com/VictorNascimento14/Arcada/pull/24
branch: feat/repositorio-local
tags: [pr, fundacao, dados]
status: merged
---

# PR #24 — feat(dados): criar o repositório local com coleções versionadas

## 🎯 Contexto

Item 0.8 (Repositório local) do módulo 0 do [[2026-09-30-plano-da-v1]]: a camada `src/dados/` que o [[ADR-001-frontend-primeiro-com-dados-locais]] pede — coleções tipadas sobre `localStorage`, com versão de esquema e assinatura para o React. Ainda sem tela nem coleção de domínio; os módulos criam as suas com `criarColecao`. Fecha a issue #8.

## 🔧 Mudanças

- `src/dados/colecao.ts` — `criarColecao` e os tipos `Colecao` e `OpcoesColecao`.
- `src/dados/useColecao.ts` — o hook `useColecao`, sobre `useSyncExternalStore`.
- `src/dados/id.ts` — `novoId`.
- Testes ao lado de cada arquivo: 38 casos (30 em `colecao.test.ts`, 4 em `useColecao.test.tsx`, 4 em `id.test.ts`).
- Nenhuma dependência nova e nenhum arquivo compartilhado alterado.

## 🕵️ Dado pessoal (LGPD)

A camada guarda no `localStorage` do navegador o que os módulos mandarem, e isso inclui dado de saúde de paciente. Nada sai do navegador (sem rede, sem servidor), e o código não escreve item em log nem em mensagem de erro: a única mensagem cita o nome da coleção. Os testes usam só `Item A`, `Item B` e `Item C`: nenhum dado real de pessoa, nenhum CPF.

## 🧠 Decisões técnicas

- **Uma chave por coleção** (`arcada:<nome>`), com o envelope `{ versao, itens }`, em vez de um estado único: cada módulo versiona e migra o seu esquema sem tocar nos outros.
- **Leitura síncrona, em memória.** O `localStorage` é gravado a cada escrita e lido na criação da coleção e quando outra aba grava; a tela nunca precisa de estado de carregamento.
- **Snapshot estável.** `listar()` devolve o mesmo array até algo ser gravado, e `remover` de um id que não existe não grava nem avisa. O hook passa `listar` direto ao `useSyncExternalStore`.
- **Migração em uma chamada**: `migrar(itensAntigos, versaoAntiga)` decide os passos e o resultado é regravado já na versão nova, então não roda de novo. Se lançar, `criarColecao` propaga o erro e o dado salvo fica intacto.
- **Só se descarta o que nem se lê** (JSON corrompido ou envelope inválido: coleção vazia). Dado de versão menor sem `migrar`, ou de versão mais nova que a do código (aba velha), passa como está. O teto conhecido, comentado no código com `ponytail:`: a aba velha que gravar carimba a versão velha por cima.
- **A escrita valida**: `salvar` e `substituirTudo` recusam item sem `id` ou com `id` repetido, antes de mexer no estado.
- **Falha de gravação não avisa a tela** (cota cheia, storage bloqueado): segue em memória nesta aba. Teto conhecido, também comentado no código: se a interface precisar dizer "não foi salvo", o erro sai por um `assinar` próprio.
- **Sem `index.ts`**: os módulos importam do arquivo que precisam, e não há arquivo compartilhado para todo módulo editar.

## ⚠️ Armadilhas e aprendizados

- `useSyncExternalStore` exige snapshot estável: filtrar ou ordenar dentro do hook devolveria um array novo a cada render e entraria em laço. Filtro e ordem se derivam na tela, com `useMemo`.
- `crypto.randomUUID` só existe em contexto seguro (https e localhost). Abrindo o `npm run dev` pelo IP da rede local no celular (http), ele é `undefined`: daí o plano B por `getRandomValues`.
- O evento `storage` só dispara nas OUTRAS abas. Duas instâncias do mesmo nome na mesma aba não se enxergam: uma coleção por nome, no escopo do módulo.
- Não é só `setItem` que lança: o simples acesso a `window.localStorage` lança `SecurityError` com o site bloqueado. Por isso todo acesso fica dentro de um `try`.
- Como a coleção lê o storage na criação, teste que semeia o storage DEPOIS de criá-la fica vazio e verde. Peguei isso nos casos de envelope corrompido, conferindo os testes por mutação do código (27 alterações, todas reprovadas agora).

## 🧪 Como testar

1. `npm test`: 38 testes novos (CRUD, persistência entre instâncias, migração de versão, storage indisponível, sincronização entre abas, snapshot estável e ids).
2. `npm run lint && npm run type-check && npm run build` sem erro.
3. `grep -rn localStorage src --include='*.ts' --include='*.tsx'` só encontra arquivos de `src/dados/`: nenhuma tela toca o storage direto.
4. Ainda não há tela que use a camada, então a verificação é pelos testes. A sincronização entre abas foi exercitada com o evento `storage` sintético do jsdom, não com duas abas reais.

## 📎 Documentação afetada

- [[RepositorioLocal]]
- [[ADR-001-frontend-primeiro-com-dados-locais]]
- [[2026]] (changelog)
