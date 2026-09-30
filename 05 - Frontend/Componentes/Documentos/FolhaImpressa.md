---
tipo: funcionalidade
camada: frontend
area: Documentos
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, documentos, impressao, assinatura]
---

# Folha impressa

## O que é

A peça que todo documento impresso do Arcada usa: `FolhaImpressa`, a folha para o papel (cabeçalho da clínica,
título, corpo e a linha para assinar à mão com o nome e o CRO do profissional), e o hook `useImpressao`, que a põe
na página, abre a impressão do navegador e a tira depois (item 12.1 do [[2026-09-30-plano-da-v1]]). É o mecanismo
de [[ImpressaoDaAnamnese]] extraído para `src/componentes/`, como o aprendizado
[[2026-09-30-css-de-impressao-do-kit-pede-um-clone-no-body]] previa. A v1 é demonstração: a folha não tem
assinatura digital nem validade jurídica, e só imprime o que foi digitado, sem sugerir conduta.

## Onde está no código

- `src/componentes/FolhaImpressa.tsx` — a folha (portal no `<body>`, `data-print-clone`).
- `src/componentes/useImpressao.ts` — `useImpressao<T>()`: o estado do pedido e o efeito que liga o modo de
  impressão, chama `window.print()` e desfaz no `afterprint`.
- `src/ui/index.css` — as regras `@media print` do kit, bloco "Exportar PDF" (não se edita).
- `src/componentes/FolhaImpressa.test.tsx`, `src/componentes/useImpressao.test.tsx` — os testes.

## Como usar

```tsx
const { folha, imprimir } = useImpressao<{ texto: string }>();

<Button onClick={() => imprimir({ texto })}>Imprimir</Button>;
{folha && (
  <FolhaImpressa titulo="Receituário" profissional={profissional}>
    {folha.texto}
  </FolhaImpressa>
)}
```

`folha` é `null` fora da impressão. `imprimir(dados)` guarda os dados, e a folha é desenhada a partir deles.

## Comportamento

- **Cabeçalho**: o nome da clínica em destaque; embaixo, `endereço — cidade/UF` e `Tel. <telefone>`, lidos do
  registro `CLINICA_ID` da coleção `clinica` (a tela [[Clinica]] os edita). Campo vazio não deixa linha nem
  separador sobrando. Sem clínica cadastrada, a folha sai sem cabeçalho.
- **Título e corpo**: o `titulo` vira o `h1`; o corpo é o que a tela passa como filho.
- **Rodapé**: a linha para assinar à mão e, abaixo, o nome e o CRO do profissional (`Pick<Profissional, "nome" |
  "cro">`, de [[Profissionais]]). Sobra espaço acima da linha para a assinatura, e a linha não fica sozinha numa
  página.
- **Só a folha sai no papel**: o resto da tela fica escondido pelo CSS do kit. Sai em preto sobre branco também com
  o tema escuro.
- **A folha só existe durante a impressão**: entra no `<body>` com o `imprimir` e sai no `afterprint`. Imprimir de
  novo funciona, mesmo sem o `afterprint`, e sair da tela com a folha no ar desliga o modo de impressão.

## Movimento e micro-interações

Nenhuma.

## Limites conhecidos

- **Quem usa**: receituário (#154), atestado (#163) e declaração de comparecimento (#169). O plano de
  tratamento e o orçamento (3.11 e 7.7) também podem usá-la.
- **A anamnese ainda tem o mecanismo próprio** (`FolhaDaAnamnese` + o efeito de `HistoricoDeVersoes`) e pode
  migrar para `useImpressao`. A folha dela assina o paciente, e `FolhaImpressa` traz o profissional no rodapé:
  migrar a folha inteira pede uma legenda de assinatura configurável; migrar só o efeito, não.
- **O rodapé é sempre o do profissional.** Sem legenda ("Assinatura do profissional") e sem carimbo: nome e CRO
  sob a linha.
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-141-documentos-folha]] — a folha com cabeçalho da clínica e linha de assinatura, e o hook de impressão extraído da anamnese.
