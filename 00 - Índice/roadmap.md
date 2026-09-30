---
tipo: indice
ultima_atualizacao: 2026-09-30
tags: [indice, roadmap]
---

# Roadmap

O que está em construção e o que vem depois. Para o que o produto é e para quem, veja
[[visao-de-produto]]; para os termos, [[glossario]].

## v1 — só no navegador, com dados fictícios (em andamento)

Quinze módulos, entregues em 108 PRs pequenos. O backlog item a item, o fluxo de cada PR e o status estão
em [[2026-09-30-plano-da-v1]].

- **0 · Fundação**
- **1 · Pacientes**
- **2 · Anamnese**
- **3 · Odontograma**
- **4 · Periodontograma**
- **5 · Clínica e equipe**
- **6 · Procedimentos**
- **7 · Plano de tratamento e orçamento**
- **8 · Agenda**
- **9 · Atendimento**
- **10 · Financeiro**
- **11 · Retornos**
- **12 · Documentos**
- **13 · Painel**
- **14 · Sistema**

A v1 fecha quando os 108 itens do plano estiverem mergeados na `main`. O que ela não cobre está em
[[visao-de-produto]].

## Depois da v1

Sem data e sem ordem de prioridade: cada item é uma direção, não um compromisso. As decisões difíceis de
reverter de cada um viram ADR ([[adrs]]) antes de virar código.

- **Backend, com login e controle de acesso por papel.** Troca o armazenamento local por servidor e banco;
  o ponto de troca é `src/dados/` ([[ADR-001-frontend-primeiro-com-dados-locais]], que será substituída
  por uma ADR nova). É o que destrava o uso com dado real de paciente: até lá, o app roda só com dado
  fictício.
- **Prontuário com assinatura digital.** Registro clínico com validade legal: assinatura digital e guarda
  do registro. Exige guarda durável, que o `localStorage` não oferece. Os requisitos legais a levantar
  antes do desenho: `<A DEFINIR>`.
- **Faturamento de convênio.** Guias no padrão TISS e envio à operadora. Na v1 só existe o cadastro dos
  convênios aceitos. Desenho: `<A DEFINIR>`.
- **Estoque.** Controle de materiais e insumos da clínica. Desenho: `<A DEFINIR>`.
- **Multiclínica.** Várias clínicas no mesmo sistema, cada uma com seus pacientes, profissionais e agenda.
  Pressupõe backend e controle de acesso.

O backend é a base dos demais: prontuário com assinatura e multiclínica pedem servidor e controle de
acesso. Para faturamento de convênio e estoque, a dependência é `<A DEFINIR>`.

Sem previsão: **emissão fiscal**, fora da v1 e ainda sem desenho (`<A DEFINIR>`).
