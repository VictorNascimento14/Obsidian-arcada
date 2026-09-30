---
tipo: funcionalidade
camada: frontend
area: Documentos
rota: /documentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, documentos, atestado, impressao]
---

# Atestado

## O que é

O segundo documento da tela **Documentos** (`/documentos`): paciente, profissional, um período com data e hora de
início e de fim, e a finalidade em texto livre, impressos numa folha com o cabeçalho da clínica e a linha para
assinar à mão (item 12.3 do [[2026-09-30-plano-da-v1]]). A v1 é demonstração, não prontuário: não há assinatura
digital nem validade jurídica, e **o app não escreve o atestado**: nenhuma frase pronta, prazo de afastamento ou
conduta. O período e a finalidade são de quem emite.

## Onde está no código

- `src/modulos/documentos/Atestado.tsx` — o cartão do atestado e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/atestado.ts` — a regra pura: `camposDoAtestado`, `prepararAtestado`, `textoDoPeriodo`,
  `LIMITE_DA_FINALIDADE`.
- `src/modulos/documentos/validacao.ts` — `resolverEscolha`, `dataValida` e `horaValida`, compartilhadas pelos
  documentos.
- `src/modulos/documentos/PacienteEProfissional.tsx` — as duas escolhas, as mesmas do [[Receituario]].
- `src/componentes/FolhaImpressa.tsx`, `src/componentes/useImpressao.ts` — a folha e o mecanismo de impressão
  ([[FolhaImpressa]]).

## Comportamento

- **Campos**: `Paciente`, `Profissional` (só os ativos, de [[Profissionais]]), `Data de início` e `Data de fim`
  (as duas em hoje ao abrir), `Hora de início` e `Hora de fim` (vazias) e `Finalidade` (vazia, até 500
  caracteres). Nada vem preenchido além das datas.
- **Imprimir** confere tudo: paciente e profissional ativo, as quatro datas e horas (que existam no calendário e no
  relógio), o **fim depois do início** (igual não vale) e a finalidade que não seja só espaço. O erro do fim vai
  para a hora de fim quando o dia é o mesmo e para a data de fim quando os dias são diferentes. Faltando algo, a
  impressão não abre.
- **A folha** tem: o cabeçalho da clínica (cadastro em [[Clinica]]); o título `Atestado`; `Paciente: <nome>`;
  `Período: 30/09/2026, das 08:00 às 12:30` (mesmo dia) ou `Período: de 30/09/2026 às 08:00 até 02/10/2026 às
  18:00` (dias diferentes); `Finalidade` e o texto, com as quebras de linha como foram digitadas; e, no fim, a linha
  para assinar à mão com o nome e o CRO do profissional. Do paciente entra só o nome: nada de CPF nem de telefone.
- **Só a folha sai no papel**, em preto sobre branco também com o tema escuro. Conferida em A4: uma página.
- **O formulário segue preenchido depois da impressão**, para imprimir outra via. Nada é gravado.

## Movimento e micro-interações

Nenhuma no cartão além das do kit (campos, botão). A folha não tem movimento.

## Limites conhecidos

- **A hora é obrigatória nas duas pontas.** Para um período de dias inteiros, o jeito é `00:00` no início e `23:59`
  no fim.
- **Não guarda nada**: fechar a tela perde o que foi digitado. Salvar e reutilizar textos é o item 12.5 do plano.
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-163-documentos-atestado]] — o atestado com período e finalidade em texto livre, impresso na folha com linha de assinatura.
