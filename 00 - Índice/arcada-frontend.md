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

### 2 · Anamnese

- [[FormularioAnamnese]] — a aba Anamnese da ficha: as perguntas do questionário, preenchidas com o paciente e salvas em versões datadas.
- [[HistoricoDeVersoes]] — as versões da anamnese de um paciente, da mais nova à mais antiga, com a leitura das respostas de cada uma e o botão Imprimir.
- [[ImpressaoDaAnamnese]] — a folha de uma versão da anamnese para o papel, com a linha para o paciente assinar à mão.
- [[SeloAlertas]] — as pílulas com os alertas da anamnese mais recente, no topo da aba, no cartão da lista e na ficha do paciente.

### 3 · Odontograma

- [[CondicoesELegenda]] — as nove condições do odontograma e a legenda com a cor de cada uma.
- [[Dente]] — o desenho de um dente em SVG, com o número FDI e as cinco faces, clicáveis e focáveis por teclado.
- [[Arcadas]] — as duas arcadas permanentes, cada dente desenhado pelo `Dente`, com a linha média no meio.
- [[Denticoes]] — o seletor que alterna o odontograma entre a dentição permanente, a decídua e a mista.
- [[MarcarCondicoes]] — a aba Odontograma da ficha: a barra de condições, a marcação por face e por dente e o símbolo de cada condição.

### 4 · Periodontograma

- [[GradeDeSondagem]] — a aba Periodonto da ficha: as duas arcadas, com a profundidade, a margem, o sangramento e a supuração de cada sítio.
- [[NavegacaoPorTeclado]] — como se percorre a grade pelo teclado (Tab e setas), e a arcada inferior que entrou junto.
- [[SangramentoESupuracao]] — as marcas de sangramento à sondagem e de supuração em cada sítio da grade.
- [[IndicesDoExame]] — os quatro cartões de índices do exame, acima da grade, recalculados a cada valor.

### 5 · Clínica e equipe

- [[Clinica]] — `/clinica`: a tela de cadastro da clínica, um cartão por assunto.
- [[Profissionais]] — o cartão da equipe: registro no CRO, especialidade e a cor na agenda.
- [[Cadeiras]] — o cartão dos postos de atendimento.
- [[Expediente]] — o cartão dos dias e horários de atendimento, com o intervalo do almoço, que a agenda lê para sugerir horários livres.
- [[Convenios]] — o cartão dos convênios aceitos: uma lista simples de nomes, com campo para acrescentar e botão para remover.

### 6 · Procedimentos

- [[ListaDeProcedimentos]] — `/procedimentos`: a tabela de procedimentos, com busca por nome e código e filtro por especialidade.
- [[CadastroDeProcedimento]] — o modal que cadastra e edita um procedimento, aberto pela lista.
- [[CatalogoPadrao]] — os 32 procedimentos comuns, em oito especialidades, que a clínica recebe na primeira abertura.
- [[ReajusteDePrecos]] — o modal que reajusta por um percentual o preço dos procedimentos selecionados, com a prévia antes de gravar.

### 7 · Plano de tratamento e orçamento

- [[PlanoDeTratamento]] — `/planos/:planoId`: os itens do plano por dente e face, o orçamento e a situação; chega-se pela aba `Tratamentos` da ficha.
- [[PlanosEmAberto]] — `/tratamentos`: os planos ainda em curso, de todos os pacientes, com a situação e o total.
- [[TotaisDoPlano]] — o subtotal, o total do orçamento e os itens já realizados.
- [[DescontoDoOrcamento]] — o desconto percentual ou em valor, guardado em centavos.
- [[ParcelamentoDoOrcamento]] — o total dividido em parcelas que somam exatamente o total, com o vencimento de cada uma.
- [[SituacaoDoPlano]] — o caminho do plano: proposto, aprovado, em andamento e concluído — ou recusado.
- [[ProgressoDoTratamento]] — quanto do plano já foi feito: uma barra com os itens realizados sobre o total e o número ao lado (`2 de 3 realizados`).

### 8 · Agenda

- [[AgendaDoDia]] — `/agenda`: o dia da clínica por cadeira.
- [[MarcarConsulta]] — o modal da agenda que marca a consulta, barra conflito e feriado e sugere os horários livres.
- [[CalendarioDoMes]] — o calendário do mês da agenda: mostra os dias que têm consulta e leva a qualquer um deles.
- [[AgendaDaSemana]] — a semana da agenda: sete colunas, de segunda a domingo, com as consultas de cada dia em ordem de horário.
- [[DetalheDaConsulta]] — o modal aberto pelo cartão da consulta: quem, quando e onde, e um botão para cada passo da situação.
- [[RemarcarECancelarConsulta]] — remarcar, que reabre o formulário de marcação com a consulta, e cancelar, que pede o motivo.
- [[ConfirmacaoPeloWhatsApp]] — o link do detalhe da consulta que abre o WhatsApp do paciente com a mensagem de confirmação pronta.

### 9 · Atendimento

- [[AtendimentoDaConsulta]] — a entrada do atendimento: da consulta marcada na agenda o paciente passa a ser atendido.
- [[EvolucaoClinica]] — o cartão do atendimento onde o profissional escreve, em texto livre, o que aconteceu na consulta.
- [[ProcedimentosRealizados]] — o cartão do atendimento onde se marca, entre os itens dos planos do paciente, o que foi realizado na consulta.

### 10 · Financeiro

- [[ContasAReceber]] — `/financeiro`: as parcelas de todos os pacientes, com a situação de cada uma e as em aberto primeiro.
- [[PlanosSemParcelas]] — os planos aprovados ou em andamento que ainda não têm parcelas, com o botão Gerar parcelas, e a aba Financeiro da ficha.
- [[GeracaoDeParcelas]] — as regras que levam um plano aprovado às parcelas: quais planos esperam, a conta das parcelas e a escrita dos lançamentos.
- [[SituacaoDaParcela]] — a regra que diz em que pé está uma parcela num dia: paga, vencida, vence hoje ou a vencer.
- [[BaixaDaParcela]] — o registro de que a parcela foi paga, com o dia e a forma do pagamento: o botão Dar baixa e o modal.
- [[Inadimplencia]] — quem deve e há quanto tempo: as parcelas vencidas e sem baixa por paciente, e o cartão da tela `/financeiro`.

### 11 · Retornos

- [[ListaDeRetornos]] — `/retornos`: quem deve voltar ao consultório, em vencidos, a vencer nos próximos 30 dias e dispensados.
- [[ContatoDoRetorno]] — os links WhatsApp e Marcar consulta de cada linha da lista de retornos.
- [[AdiarEDispensarRetorno]] — os botões Adiar e Dispensar de cada linha, o motivo da dispensa e o cartão dos dispensados, com Reativar.

### 12 · Documentos

- [[Documentos]] — `/documentos`: a tela dos documentos impressos (receituário, atestado e declaração), no grupo Gestão da coluna lateral.
- [[FolhaImpressa]] — a folha para o papel — cabeçalho da clínica, título, corpo e linha para assinar — e o hook `useImpressao` que a imprime.
- [[Receituario]] — o primeiro documento da tela: um texto livre, com paciente, profissional e data, impresso na folha com a linha para assinar.
- [[Atestado]] — o segundo documento: um período, com data e hora de início e de fim, e a finalidade em texto livre.
- [[Declaracao]] — o terceiro documento: a declaração de comparecimento, com o dia e o horário, que podem vir de uma consulta do paciente.

### 13 · Painel

- [[PainelDoConsultorio]] — `/`: a tela inicial do Arcada, o consultório num relance, montada por quatro blocos.
- [[IndicadoresDoMes]] — o faturamento recebido, as consultas e a taxa de faltas do mês, cada um num cartão com o número animado.
- [[TratamentosEmAberto]] — a contagem e o valor dos orçamentos à espera do paciente e dos tratamentos aprovados ou em andamento.
- [[ConsultasDeHoje]] — as consultas de hoje em ordem de horário, com a situação, e a próxima em destaque.
- [[FaturamentoPorSemana]] — o recebido em cada uma das últimas seis semanas, em barras.

### 14 · Sistema

- [[Sistema]] — `/sistema`: a tela de sistema, no grupo Cadastros da coluna lateral, com os cartões de backup, restauração da demonstração e busca.
- [[RestauracaoDaDemonstracao]] — o cartão que apaga o que o navegador guarda do Arcada e recarrega a página para as sementes plantarem a demonstração de novo.
- [[BuscaGlobal]] — a caixa de busca aberta por `Ctrl+K` (`⌘K` no Mac): o nome ou o telefone de um paciente e Enter abre a ficha, em qualquer tela.

## Fluxos

- [[AtivarEDesativarProcedimento]] — como a clínica tira um procedimento das escolhas sem apagá-lo: fica na tabela e no histórico, mas some do que se oferece para itens novos.
- [[ProcedimentoRealizadoNoOdontograma]] — o elo entre o que foi feito e o desenho do dente: marcar um item do plano como realizado aplica a condição do procedimento no odontograma do paciente.
