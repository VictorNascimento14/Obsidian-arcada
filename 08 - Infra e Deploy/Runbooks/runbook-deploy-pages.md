---
tipo: runbook
camada: infra
escopo: publicar a demo do Arcada no GitHub Pages, conferir o deploy e resolver o que trava
ultima_atualizacao: 2026-09-30
tags: [runbook, infra, deploy]
---

# Runbook — deploy da demo no GitHub Pages

A demo fica em **https://victornascimento14.github.io/Arcada/** e se atualiza sozinha a cada merge na
`main`. O que roda é o workflow `Pages` (`.github/workflows/pages.yml` no repositório de código); o item
que o criou é o 0.7 do [[2026-09-30-plano-da-v1]].

## Quando usar

- Para saber **como a demo chega ao ar** e o que conferir depois de um merge.
- Para **publicar de novo** sem commit novo (a demo ficou desatualizada, ou o último deploy falhou).
- Ao **ligar o Pages numa cópia nova** do repositório (outro fork, outra conta).
- Quando o run `Pages` fica vermelho ou a demo abre em branco.

## Como funciona

`push` na `main` (ou disparo manual) → job `build` → job `deploy`.

1. **`build`**: `npm ci`; `npm run build` com `BASE_PATH=/Arcada/`; `cp out/index.html out/404.html`;
   sobe `out/` como artefato do Pages.
2. **`deploy`**: `actions/deploy-pages@v4` no ambiente `github-pages`, que devolve a URL.

Duas decisões que não são óbvias:

- **`BASE_PATH=/Arcada/`.** O site mora numa subpasta do domínio, não na raiz. O `vite.config.ts` lê
  `BASE_PATH` e prefixa todo asset com ele; sem a variável o build sai com `/assets/...` e o navegador
  procura `victornascimento14.github.io/assets/...`, que não existe. O build local (`npm run build`)
  continua na raiz de propósito.
- **`404.html` igual ao `index.html`.** O Pages não tem fallback de SPA, mas serve o `404.html` para
  qualquer caminho que não existe. Com o conteúdo do `index.html`, abrir ou recarregar
  `/Arcada/alguma/rota` carrega o app e o roteador assume a rota, em vez da página de erro do GitHub.
  Os assets do `404.html` são absolutos (`/Arcada/assets/...`), então funcionam em qualquer
  profundidade.

## Pré-requisitos (uma vez por repositório)

O Pages precisa estar ligado com a origem **GitHub Actions** — o workflow não faz esse passo. Pela
interface: Settings → Pages → Build and deployment → Source: **GitHub Actions**. Pela CLI:

```bash
gh api repos/VictorNascimento14/Arcada/pages -X POST -f build_type=workflow
```

Se o site de Pages já existir com outra origem, troque o `-X POST` por `-X PUT`. Conferência:

```bash
gh api repos/VictorNascimento14/Arcada/pages --jq '.build_type + " " + .html_url'
# workflow https://victornascimento14.github.io/Arcada/
```

Ligado em 2026-09-30. Em conta gratuita o Pages só serve repositório público — o do Arcada é público.

O GitHub cria sozinho o ambiente `github-pages` com a política de deploy **só a partir da `main`**:
disparar o workflow de outra branch é recusado, e é o comportamento esperado.

## Passos

1. **Deploy normal:** mergeie o PR na `main`. O workflow `Pages` dispara junto com o CI da `main`.
2. **Acompanhar:**
   ```bash
   gh run list -R VictorNascimento14/Arcada -w pages.yml -L 3
   gh run watch <id> -R VictorNascimento14/Arcada
   ```
3. **Publicar de novo sem commit novo:**
   ```bash
   gh workflow run pages.yml -R VictorNascimento14/Arcada --ref main
   ```
   Também vale o botão **Run workflow** na aba Actions, escolhendo a `main`.
4. **Conferir o build de produção antes de mergear** (o deploy em si só roda na `main`):
   ```bash
   BASE_PATH=/Arcada/ npm run build
   grep '/Arcada/assets/' out/index.html
   ```
   Têm de aparecer duas linhas — o `<script>` e o `<link>` do CSS —, ambas com `/Arcada/assets/`.

## Como saber que deu certo

- O run `Pages` termina verde, com os jobs `build` e `deploy`; o ambiente `github-pages` mostra a URL.
- `curl -s -o /dev/null -w '%{http_code}\n' https://victornascimento14.github.io/Arcada/` imprime `200`,
  e a página abre no navegador com o estilo aplicado e sem `404` de `/assets/` no console.
- **Rota interna** (só a partir do PR da casca, quando o roteador existir): abrir direto
  `https://victornascimento14.github.io/Arcada/<rota>` e recarregar nela mostra o app, não a página de
  erro do GitHub. O **status HTTP dessa resposta continua 404** — o Pages só troca o corpo pelo
  `404.html`; por isso `curl -I` mostra 404 mesmo com tudo certo. Confira pelo navegador, ou pelo corpo:
  `curl -s https://victornascimento14.github.io/Arcada/<rota> | grep '<title>Arcada</title>'`.

## Se der errado

| Sintoma | Causa provável | O que fazer |
|---|---|---|
| O job `deploy` falha logo no começo | Pages desligado, ou com origem diferente de GitHub Actions | Rodar o comando de **Pré-requisitos**; `build_type` tem de ser `workflow` |
| O job `deploy` é recusado pelo ambiente | Workflow disparado de uma branch que não é a `main` | Disparar de novo com `--ref main` |
| A página abre em branco e o console mostra `404` em `/assets/...` (sem `/Arcada/`) | O build saiu sem `BASE_PATH` | Conferir o `env` do passo `npm run build` no `pages.yml` |
| O app abre numa rota interna, mas nenhuma rota casa | O roteador não usa `basename` | Ver a pendência abaixo |
| O run `Pages` fica aguardando | `concurrency: pages`: um deploy por vez, e o que começou termina (`cancel-in-progress: false`). Com vários merges seguidos, só o run mais novo espera na fila — os pendentes intermediários são cancelados, e o site termina na `main` mais recente | Esperar |

## Pendência — PR da casca (item 0.6)

O roteador ainda não existe. Quando entrar, ele precisa de
`basename: import.meta.env.BASE_URL` (por exemplo
`createBrowserRouter(rotas, { basename: import.meta.env.BASE_URL })`). O Vite preenche `BASE_URL` com o
`BASE_PATH` do build: `/Arcada/` no build do Pages, `/` no `npm run dev`. Sem o `basename`, a rota `/pacientes`
do roteador nunca casa com a URL publicada `/Arcada/pacientes` — a demo abre sem tela. É também o que
permite provar o critério "recarregar numa rota interna não dá 404" da issue #7, que só faz sentido com
o roteador no ar.

## Histórico

- 2026-09-30 — criado com o workflow `Pages`, em [[2026-09-30-pr-021-deploy-pages]].

Relacionados: [[runbook-rodar-local]] (checks e build local).
