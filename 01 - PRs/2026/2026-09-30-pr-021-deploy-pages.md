---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 21
url: https://github.com/VictorNascimento14/Arcada/pull/21
branch: chore/deploy-pages
tags: [pr, fundacao, deploy]
status: aberto
---

# PR #21 — chore(deploy): publicar a demo no GitHub Pages a cada merge na main

## 🎯 Contexto

Item 0.7 (Deploy no GitHub Pages) do módulo 0 do [[2026-09-30-plano-da-v1]]; critérios da issue #7: a demo abre em `victornascimento14.github.io/Arcada/` e recarregar numa rota interna não dá 404. Fecha a issue #7.

## 🔧 Mudanças

- `.github/workflows/pages.yml` — dispara em `push` na `main` e em `workflow_dispatch`, com `permissions` `contents: read`, `pages: write` e `id-token: write` e `concurrency: pages` sem cancelar o que já começou. Job `build`: checkout, Node 22 com cache npm, `npm ci`, `npm run build` com `BASE_PATH=/Arcada/`, `cp out/index.html out/404.html` e `upload-pages-artifact@v3` com `out/`. Job `deploy`: `deploy-pages@v4` no ambiente `github-pages`.
- GitHub Pages do repositório ligado com a origem “GitHub Actions” (`gh api repos/VictorNascimento14/Arcada/pages -X POST -f build_type=workflow`). O GitHub criou o ambiente `github-pages` restrito à branch `main`.
- Runbook novo, [[runbook-deploy-pages]]: como o deploy funciona, como ligar o Pages, como publicar de novo e o que fazer quando falha.

## 🧠 Decisões técnicas

- `BASE_PATH=/Arcada/` só no workflow, não no `vite.config.ts`: o `npm run dev` e o `npm run build` locais continuam na raiz. O config já lia `BASE_PATH` desde o scaffolding ([[2026-09-30-pr-013-scaffolding]]).
- `404.html` igual ao `index.html`: o Pages não tem fallback de SPA, mas serve o `404.html` para caminho inexistente. Com o conteúdo do `index.html` o app carrega e o roteador assume a rota; os assets do build são absolutos (`/Arcada/assets/...`), então funcionam em qualquer profundidade.
- O workflow não repete lint, type-check e test: o PR já passou pelo CI antes do merge. Dispara só em `push` na `main`.
- `concurrency: pages` com `cancel-in-progress: false`: um deploy por vez, e o que já começou termina em vez de ser cancelado no meio.
- Versões das actions: `checkout@v4` e `setup-node@v4` como no `ci.yml`; `upload-pages-artifact@v3` e `deploy-pages@v4` como pedido no item. As versões atuais são a v5 (as duas). O CI já mostra o aviso de que actions de Node 20 (`checkout@v4`, `setup-node@v4`) rodam forçadas em Node 24 e passam; o mesmo é esperado das do Pages, mas só o primeiro deploy comprova. Subir as majors fica para um PR à parte.

## ⚠️ Armadilhas e aprendizados

- **Pendência do PR da casca (item 0.6):** o roteador ainda não existe. Quando entrar, precisa de `basename: import.meta.env.BASE_URL` (por exemplo `createBrowserRouter(rotas, { basename: import.meta.env.BASE_URL })`); sem isso a rota `/pacientes` nunca casa com a URL publicada `/Arcada/pacientes` e a demo abre sem tela. É também o que permite provar o critério “recarregar numa rota interna não dá 404”. Registrado em [[runbook-deploy-pages]].
- O `404.html` resolve o recarregamento, mas o status HTTP da rota interna continua 404 — só o corpo é o app. `curl -I` mostra 404 mesmo com tudo certo; confira pelo navegador ou pelo corpo.
- O deploy só se exercita depois do merge: `workflow_dispatch` exige o arquivo na branch padrão e o ambiente `github-pages` só aceita a `main`. Antes do merge foram provados o build com `BASE_PATH` (as referências saem em `/Arcada/assets/...`), o `404.html` idêntico ao `index.html`, o YAML lendo certo (gatilhos, permissões, jobs) e, num servidor local que imita o Pages, os assets em `/Arcada/assets/` e uma rota interna devolvendo o `404.html`; o run em si e a URL no ar não.

## 🧪 Como testar

1. `BASE_PATH=/Arcada/ npm run build && grep '/Arcada/assets/' out/index.html` → duas linhas, o `<script>` e o `<link>` do CSS, ambas em `/Arcada/assets/...` (sem `BASE_PATH`, o build sai em `/assets/...`).
2. `gh api repos/VictorNascimento14/Arcada/pages --jq '.build_type + " " + .html_url'` → `workflow https://victornascimento14.github.io/Arcada/`.
3. Depois do merge: o run **Pages** da `main` termina verde (jobs `build` e `deploy`) e a URL acima abre o app com o estilo aplicado.
4. `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[runbook-deploy-pages]]
- [[2026]] (changelog)
