---
tipo: runbook
camada: frontend
escopo: rodar, verificar e gerar o build do Arcada na máquina
ultima_atualizacao: 2026-09-30
tags: [runbook, frontend]
tempo_estimado: 5 min
---

# Runbook — rodar o Arcada local

## Quando usar

Primeira vez numa máquina, depois de trocar de branch com `package.json` diferente, ou antes de abrir PR
(os checks locais são os mesmos que o CI roda).

O `package.json` chega no PR de scaffolding, o primeiro item do módulo 0 do
[[2026-09-30-plano-da-v1]]; até lá não há o que instalar.

## Pré-requisitos

- Node.js e npm. A versão mínima é a do `package.json` (`TODO: confirmar o campo engines no PR de
  scaffolding`).
- Git, com acesso aos repositórios `VictorNascimento14/Arcada` (código) e
  `VictorNascimento14/Obsidian-arcada` (este cofre).

## Variáveis de ambiente

Duas variáveis dizem onde está cada clone. Defina-as **uma vez por máquina**, no shell profile
(`~/.bashrc`, `~/.zshrc` ou equivalente) — nunca no repositório:

```bash
export ARCADA_REPO="<pasta do clone de VictorNascimento14/Arcada>"
export ARCADA_VAULT="<pasta do clone de VictorNascimento14/Obsidian-arcada>"
```

- `ARCADA_REPO` — o código (React + Vite).
- `ARCADA_VAULT` — este cofre.

Abra um shell novo (ou rode `source` no profile) e confira:

```bash
[ -d "$ARCADA_REPO" ] && [ -d "$ARCADA_VAULT/00 - Índice" ] && echo ok
```

Cada máquina registra a sua linha em [[caminho-canonico-do-cofre]].

Se `ARCADA_VAULT` estiver vazia ou não apontar para o cofre, **pare e avise**: nunca documente em outro
lugar. Se `echo "$ARCADA_REPO"` sair vazio num script ou ferramenta que abre shell sem interação, esse
shell não leu o profile: exporte a variável na chamada ou no arquivo que aquele shell lê.

## Passos

1. Entre na pasta do código: `cd "$ARCADA_REPO"`
2. Instale as dependências: `npm install`
3. Suba o servidor de desenvolvimento: `npm run dev`. Ele abre em `http://localhost:3000`.
4. Rode os checks, os mesmos que o CI roda:
   - `npm run lint`
   - `npm run type-check`
   - `npm test`
   - `npm run build` — gera o site estático em `out/`, pasta que o Git ignora.

   Tudo de uma vez, antes de abrir PR: `npm run lint && npm run type-check && npm test && npm run build`
5. Antes de escrever documentação, atualize o cofre: `cd "$ARCADA_VAULT" && git pull --rebase`

## Como saber que deu certo

- A conferência das variáveis imprime `ok`.
- `npm run dev`: a página abre em `http://localhost:3000` sem erro no console do navegador.
- `npm run lint`, `npm run type-check` e `npm test` terminam com código de saída 0.
- `npm run build` termina sem erro e cria `out/` com o `index.html`.
