---
tipo: funcionalidade
camada: frontend
area: Comuns
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, fundacao, dados]
---

# RepositorioLocal

## O que é

A camada de dados da v1: **coleções tipadas sobre o `localStorage` do navegador**, com versão de esquema, assinatura para o React e sincronização entre abas. É a fronteira que o [[ADR-001-frontend-primeiro-com-dados-locais]] exige: a tela lê por hook e escreve por função de `src/dados/`, e **nunca** toca o `localStorage`. Trocar o armazenamento por uma API mexe atrás dela, e as telas não mudam.

Esta peça não traz coleção de domínio: as oito do núcleo (paciente, consulta, plano…) estão em
`src/dados/colecoes.ts` ([[2026-09-30-pr-035-colecoes-e-sementes]]), e o dado que só um módulo usa nasce no
módulo, com `criarColecao`.

## Onde está no código

- `src/dados/colecao.ts` — `criarColecao` e os tipos `Colecao` e `OpcoesColecao`.
- `src/dados/useColecao.ts` — o hook `useColecao(colecao)`.
- `src/dados/id.ts` — `novoId()`.
- Testes ao lado de cada arquivo (`*.test.ts`, `*.test.tsx`).
- Sobre esta camada, em `src/dados/`: as coleções do núcleo (`colecoes.ts`) e os dados de demonstração
  (`sementes.ts` e `semeadores.ts`), do PR [[2026-09-30-pr-035-colecoes-e-sementes]].

## Comportamento

### Criar uma coleção

```ts
// src/dados/exemplos.ts — UMA coleção por nome, no escopo do módulo
import { criarColecao } from "./colecao";

export interface Exemplo {
  id: string;
  nome: string;
}
export const exemplos = criarColecao<Exemplo>("exemplos");
```

A coleção guarda tudo em `localStorage["arcada:<nome>"]`, no envelope `{ "versao": 1, "itens": [...] }`, e espelha o conteúdo num array em memória. A leitura é síncrona: nenhuma tela precisa de estado de carregamento. O storage é lido na criação da coleção e de novo quando outra aba grava.

### Ler e escrever

| Função | O que faz |
|---|---|
| `listar()` | Os itens, na ordem em que entraram. É o **mesmo array** até algo ser gravado. |
| `obter(id)` | O item, ou `undefined`. |
| `salvar(item)` | Insere no fim, ou substitui no mesmo lugar se o `id` já existe. |
| `remover(id)` | Tira o item. `id` que não existe é ignorado: não grava e não avisa. |
| `substituirTudo(itens)` | Troca o conteúdo inteiro (semente, importação de backup, restaurar a demonstração). |
| `assinar(fn)` | Chama `fn` a cada mudança, inclusive as vindas de outra aba. Devolve a função que cancela. |

`salvar` e `substituirTudo` recusam (lançam) item sem `id` ou com `id` repetido, sem mexer no estado. Um item que não vira JSON (ciclo, BigInt) também lança antes de qualquer mudança. Os itens são **imutáveis**: para mudar um, `salvar` um objeto novo.

Ids novos vêm de `novoId()`, um UUID v4 (`crypto.randomUUID()`). Fora de contexto seguro — o `npm run dev` aberto por http, pelo IP da rede local, no celular — `randomUUID` não existe, e a função monta o v4 com `getRandomValues`.

### Na tela

```tsx
const lista = useColecao(exemplos); // reativo
const ordenados = useMemo(() => [...lista].sort((a, b) => a.nome.localeCompare(b.nome)), [lista]);
```

O hook é o `useSyncExternalStore` sobre `assinar` e `listar`. O snapshot é o próprio array da coleção; filtro e ordenação se derivam na tela, com `useMemo`. Dentro do hook eles devolveriam um array novo a cada render, e o React entraria em laço.

### Versão de esquema

`criarColecao(nome, { versao, migrar })`, com `versao` 1 por padrão. Quando o formato dos itens muda, suba a `versao` e entregue o `migrar`:

```ts
export const exemplos = criarColecao<Exemplo>("exemplos", {
  versao: 2,
  migrar: (antigos, versaoAntiga) =>
    (antigos as ExemploV1[]).map((x) => ({ id: x.id, nome: x.titulo })),
});
```

- `migrar(itensAntigos, versaoAntiga)` recebe **todos** os itens salvos numa versão menor e devolve os itens no formato atual. Os passos (1→2→3) são de quem migra, pela `versaoAntiga`.
- O resultado é regravado já na versão nova, então a migração roda uma vez.
- Se `migrar` lançar, `criarColecao` propaga o erro e o dado salvo continua intacto.
- Só se descarta o que nem se lê: JSON corrompido ou envelope inválido dão coleção vazia. Sem `migrar`, ou com dado de versão **mais nova** que a do código (uma aba velha depois de um deploy), os itens passam como estão.

### Sem `localStorage`

Modo privado com o site bloqueado, cota cheia, webview sem storage: a coleção **segue em memória**, sem quebrar, e o dado se perde ao recarregar. A tela não é avisada.

### Entre abas

Quando outra aba grava (o evento `storage` chega só às outras abas), a coleção relê a chave, troca o array e avisa quem assinou. Um `localStorage.clear()` em outra aba esvazia a coleção.

## Limites conhecidos

- **Cota do `localStorage`**: alguns MB por origem, conforme o navegador. Se odontogramas e exames chegarem lá, o lugar da troca é atrás de `src/dados/` ([[ADR-001-frontend-primeiro-com-dados-locais]]).
- **Falha de gravação não avisa a tela.** Se a interface precisar dizer "não foi salvo", o erro sai por um `assinar` próprio (comentário `ponytail:` no código).
- **Aba velha grava por cima**: com dado de versão mais nova, ela lê como está e, se gravar, carimba a versão velha. Se doer, recusar a escrita nesse caso (também `ponytail:`).
- **Uma coleção por nome.** O evento `storage` não dispara na aba que gravou, então duas instâncias do mesmo nome na mesma aba não se enxergam.
- **Sem backup ainda.** Exportar e importar é o módulo 14 (Sistema); `substituirTudo` existe para essa importação.

## Histórico de mudanças

- [[2026-09-30-pr-024-repositorio-local]] — criação: coleções com versão de esquema, `useColecao` e `novoId`.
- [[2026-09-30-pr-035-colecoes-e-sementes]] — as coleções do núcleo e os semeadores nascem sobre esta camada, sem alterá-la.
