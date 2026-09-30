---
tipo: funcionalidade
camada: frontend
area: Documentos
rota: /documentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, documentos, impressao]
---

# Documentos (tela)

## O que é

A tela de documentos impressos, no grupo **Gestão** da coluna lateral. Cada documento é um cartão, e os cartões
ficam empilhados na mesma página: o [[Receituario]] (texto livre), o [[Atestado]] (período e finalidade) e a
[[Declaracao]] (dia e horário de comparecimento). Todos saem na mesma folha ([[FolhaImpressa]]): cabeçalho da
clínica, o corpo e a linha para o profissional assinar à mão, com nome e CRO. A v1 é demonstração, não prontuário:
não há assinatura digital nem validade jurídica (a tela avisa), e **o app não sugere conduta clínica**: nenhum
medicamento, dose, prazo de afastamento ou texto de atestado vem preenchido (item 12 do [[2026-09-30-plano-da-v1]]).

## Onde está no código

- `src/modulos/documentos/modulo.ts` — registra a rota `/documentos` e o item da coluna (grupo `gestao`, ordem 30,
  ícone `file`); o registro acha o módulo sozinho ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- `src/modulos/documentos/PaginaDocumentos.tsx` — a página: `PageShell`, o aviso de demonstração e os cartões.
- `src/modulos/documentos/Receituario.tsx`, `Atestado.tsx`, `Declaracao.tsx` — os cartões, cada um com o seu estado
  e a sua regra pura (`receituario.ts`, `atestado.ts`, `declaracao.ts`).
- `src/modulos/documentos/validacao.ts` — o que os três validam do mesmo jeito: `resolverEscolha` (paciente e
  profissional ativo), `dataValida`, `horaValida`.
- `src/modulos/documentos/PacienteEProfissional.tsx`, `Selecao.tsx` — as duas escolhas de todo documento e o
  `<select>` do sistema.
- `src/componentes/FolhaImpressa.tsx`, `src/componentes/useImpressao.ts` — a folha e o mecanismo de impressão.
- Dados: lê `pacientes`, `profissionais`, `clinica` e `consultas` (coleções do núcleo, `src/dados/colecoes.ts`).
  Nada é gravado.

## Comportamento

- **Cada cartão é um formulário com nome** (`Receituário`, `Atestado`, `Declaração de comparecimento`), para o leitor
  de tela distinguir os `Paciente` e `Profissional` de cada um.
- **Paciente e profissional se escolhem em cada cartão**; só os profissionais **ativos** aparecem, e a regra também
  recusa quem foi desativado depois de escolhido.
- **Imprimir** confere o formulário do cartão e, estando tudo certo, abre a impressão só com a folha; faltando algo, o
  erro aparece no campo e a impressão não abre. Depois da impressão o formulário segue preenchido, para outra via.
- **Só entra na folha o que o documento precisa**: o nome do paciente, nunca CPF nem telefone.

## Movimento e micro-interações

Nenhuma além das do kit. A entrada da página é a do `PageShell`.

## Limites conhecidos

- **A página é longa**: três cartões grandes empilhados. Se ganhar mais documentos, o próximo degrau são abas ou
  cartões que dobram.
- **Nada é salvo**: fechar a tela perde o que foi digitado. Salvar e reutilizar textos é o item 12.5 do plano.
- **Paciente e profissional não se compartilham entre os cartões**: cada documento tem o seu estado.
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-169-documentos-declaracao]] — a tela ganha a declaração de comparecimento, o último dos três documentos.
