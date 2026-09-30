---
tipo: funcionalidade
camada: frontend
area: Anamnese
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, anamnese, impressao, assinatura]
---

# Impressão da anamnese

## O que é

A folha de uma versão da anamnese para o papel, com a linha para o paciente assinar à mão, aberta pelo botão
**Imprimir** de cada linha do [[HistoricoDeVersoes]] (item 2.6 do [[2026-09-30-plano-da-v1]]). A v1 é
demonstração, não prontuário: a folha não tem assinatura digital nem validade jurídica, e só repete o que foi
respondido, sem sugerir conduta. O mecanismo (clone no `<body>` e modo de impressão do kit) está em
[[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]]. O mesmo mecanismo foi extraído para [[FolhaImpressa]] e o hook `useImpressao` (`src/componentes/`, PR #141), que os documentos da tela [[Documentos]] usam para imprimir; a anamnese continua com o seu (`FolhaDaAnamnese` e o efeito de `HistoricoDeVersoes`) e pode migrar.

## Onde está no código

- `src/modulos/anamnese/FolhaDaAnamnese.tsx` — a folha (portal no `<body>`, `data-print-clone`).
- `src/modulos/anamnese/HistoricoDeVersoes.tsx` — o botão `Imprimir`, o estado da folha e o efeito que abre a
  impressão e a desfaz.
- `src/modulos/anamnese/RespostasDaAnamnese.tsx` — as respostas, com as variantes `print:` (preto, corpo menor).
- `src/ui/index.css` — as regras `@media print` do kit, bloco "Exportar PDF".

## Comportamento

- **Imprimir** (`Imprimir a versão N`, na linha de cada versão) abre a impressão do navegador com a folha
  daquela versão — a mais nova ou uma anterior.
- **A folha** tem: o título `Anamnese`; `Paciente: <nome>`; `Data: DD/MM/AAAA · Versão N`; as cinco seções, com
  todas as perguntas em duas colunas (`Não respondida` e `Não informado` incluídos); e, no fim, a linha com
  `Assinatura do paciente`. Da ficha entra só o nome: nada de CPF nem de telefone.
- **Só a folha sai no papel**: o resto da tela (coluna, cabeçalho, barra de baixo, avisos) fica escondido pelo
  CSS do kit. Sai em preto sobre branco também com o tema escuro, e a anamnese inteira cabe numa página A4 com a
  assinatura; a linha não fica sozinha numa página (`break-before-avoid`).
- **A folha só existe durante a impressão**: entra no `<body>` ao clicar e sai quando o navegador dispara o
  `afterprint` (ao fechar o diálogo, imprimindo ou cancelando). Imprimir a mesma versão de novo funciona, mesmo
  se o `afterprint` não vier.
- **Sem o paciente** (registro removido), o botão fica desabilitado.

## Movimento e micro-interações

Nenhuma na folha. O botão é o `Button` ghost do kit, com o ícone de impressora (`ri-printer-line`).

## Limites conhecidos

- **Só o histórico imprime**: não há botão no formulário. O fluxo é salvar, aparecer a linha nova (`Vigente`) e
  imprimir.
- **O tamanho do papel é o do navegador** (o kit só fixa a margem, de 14 mm): a folha foi conferida em A4 e
  cabe numa página; um texto de resposta muito longo pode levá-la a duas.
- **Com "gráficos de segundo plano" ligado**, o navegador imprime o fundo do `<body>`, escuro no tema escuro; o
  padrão é não imprimir.
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-131-anamnese-impressao]] — a folha da anamnese com a linha de assinatura, aberta pelo Imprimir do histórico.
- [[2026-09-30-pr-141-documentos-folha]] — o mecanismo de impressão foi extraído para [[FolhaImpressa]] e `useImpressao`; a anamnese segue com o seu e pode migrar.
