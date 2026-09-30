---
tipo: indice
ultima_atualizacao: 2026-09-30
tags: [indice, frontend]
camada: frontend
---

# Arcada — front-end

Mapa das telas e peças do app. Cada módulo mora em `src/modulos/<modulo>/` e se registra sozinho
(rota, item da coluna e aba da ficha do paciente).

## Casca e fundação

A casca (coluna lateral, cabeçalho, barra do celular), a camada de dados e o que os módulos compartilham:
o módulo 0 do [[2026-09-30-plano-da-v1]].

- [[RailLayout]] — a coluna lateral, montada uma vez, com a navegação, a conta e o menu do celular.
- [[RepositorioLocal]] — as coleções sobre o `localStorage`, com versão de esquema, o hook `useColecao` e `novoId`.
- [[TiposDoDominio]] — os registros que os módulos compartilham (paciente, consulta, plano de tratamento, lançamento…), em `src/dominio/`.
- [[Dinheiro]] — o dinheiro em centavos: `formatarReais`, `paraCentavos` e `somarCentavos`.
- [[NotacaoFdi]] — a notação FDI do dente: que números existem e o que cada um diz — quadrante, dentição, arcada, lado, tipo e nome.
- [[FacesDoDente]] — quais faces cada dente tem e como se nomeiam.

## Módulos

Um bloco por módulo, na numeração do [[2026-09-30-plano-da-v1]]; módulo sem nota ainda não aparece.

### 1 · Pacientes

- [[ListaDePacientes]] — `/pacientes`: os pacientes em cartões, com busca por nome e telefone.
- [[CadastroDePaciente]] — `/pacientes/novo`: o formulário do paciente novo, com CPF validado.
- [[FichaDoPaciente]] — `/pacientes/:id`: o paciente, como falar com ele e, em abas, o que cada módulo guarda sobre ele.
- [[CpfDoPaciente]] — as regras puras de CPF: limpar, mascarar e validar.
- [[IdadeEFaixaEtaria]] — a idade e a faixa etária pela data de nascimento.
- [[ContatoRapido]] — os links de WhatsApp e de ligação do paciente.

### 3 · Odontograma

- [[CondicoesELegenda]] — as nove condições do odontograma e a legenda com a cor de cada uma.
- [[Dente]] — o desenho de um dente em SVG, com o número FDI e as cinco faces, clicáveis e focáveis por teclado.
- [[Arcadas]] — as duas arcadas permanentes, cada dente desenhado pelo `Dente`, com a linha média no meio.
- [[Denticoes]] — o seletor que alterna o odontograma entre a dentição permanente, a decídua e a mista.

### 5 · Clínica e equipe

- [[Clinica]] — `/clinica`: a tela de cadastro da clínica, um cartão por assunto.
- [[Profissionais]] — o cartão da equipe: registro no CRO, especialidade e a cor na agenda.
- [[Cadeiras]] — o cartão dos postos de atendimento.

### 6 · Procedimentos

- [[ListaDeProcedimentos]] — `/procedimentos`: a tabela de procedimentos, com busca por nome e código e filtro por especialidade.
- [[CadastroDeProcedimento]] — o modal que cadastra e edita um procedimento, aberto pela lista.
- [[CatalogoPadrao]] — os 32 procedimentos comuns, em oito especialidades, que a clínica recebe na primeira abertura.

### 7 · Plano de tratamento e orçamento

- [[PlanoDeTratamento]] — `/planos/:planoId`: os itens do plano por dente e face, o orçamento e a situação; chega-se pela aba `Tratamentos` da ficha.
- [[PlanosEmAberto]] — `/tratamentos`: os planos ainda em curso, de todos os pacientes, com a situação e o total.
- [[TotaisDoPlano]] — o subtotal, o total do orçamento e os itens já realizados.
- [[DescontoDoOrcamento]] — o desconto percentual ou em valor, guardado em centavos.
- [[ParcelamentoDoOrcamento]] — o total dividido em parcelas que somam exatamente o total, com o vencimento de cada uma.
- [[SituacaoDoPlano]] — o caminho do plano: proposto, aprovado, em andamento e concluído — ou recusado.

### 8 · Agenda

- [[AgendaDoDia]] — `/agenda`: o dia da clínica por cadeira.
- [[MarcarConsulta]] — o modal da agenda que marca a consulta, barra conflito e feriado e sugere os horários livres.
- [[CalendarioDoMes]] — o calendário do mês da agenda: mostra os dias que têm consulta e leva a qualquer um deles.

## Fluxos
