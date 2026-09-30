---
tipo: glossario
ultima_atualizacao: 2026-09-30
tags: [glossario, dominio]
---

# Glossário

Termos do consultório odontológico e do produto, com o significado que têm **no Arcada**. O glossário
descreve: não classifica e não recomenda conduta clínica. Onde o produto ainda não decidiu algo, está
escrito `<A DEFINIR>`. Escala de classificação clínica não é nomeada aqui; quando um módulo adotar uma, o PR
dele a define neste glossário.

Visão geral em [[visao-de-produto]]; módulos e backlog em [[2026-09-30-plano-da-v1]]; os tipos do código
que espelham estes termos, em [[TiposDoDominio]].

## Paciente e anamnese

- **Ficha do paciente** — A tela que reúne tudo de um paciente, em abas. Cada módulo que guarda dado por
  paciente registra a sua aba ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- **Idade e faixa etária** — A idade é contada em anos completos pela data de nascimento: no dia do
  aniversário o ano já conta, e quem nasceu em 29/02 completa o ano em 1º/03 nos anos não bissextos. A
  **faixa etária** agrupa a idade: **criança** até 11 anos, **adolescente** de 12 a 17, **adulto** de 18 a 59
  e **idoso** a partir de 60. É só um agrupamento por idade ([[IdadeEFaixaEtaria]]).
- **Anamnese** — Questionário sobre a saúde e o histórico do paciente: o que ele conta ao profissional
  antes e durante o tratamento. No Arcada ela mora na ficha, guarda o **histórico de versões** e imprime
  com linha para assinatura à mão. O modelo tem cinco seções — saúde geral, medicamentos em uso, alergias,
  hábitos e histórico odontológico — e 19 perguntas: as de sim ou não (algumas com um campo de detalhe, como
  "Qual?") e as de texto. Toda pergunta de sim ou não precisa de resposta para gravar: "não respondeu" não é
  "não" ([[2026-09-30-pr-082-anamnese-questionario]]).
- **Versão (da anamnese)** — Cada salvamento do formulário grava uma versão nova da anamnese, datada, sem apagar
  a anterior: a mais recente é a **vigente**, e as outras ficam no histórico, só para leitura. O formulário abre
  com as respostas da vigente, e os alertas valem só para ela ([[FormularioAnamnese]], [[HistoricoDeVersoes]]).
- **Alerta (da anamnese)** — Aviso derivado de uma resposta da anamnese, mostrado num selo no cartão e na
  ficha do paciente. O alerta **só repete o que foi respondido** ("marcou alergia a …"); não decide
  tratamento, dose nem contraindicação.

## Dentes, arcadas e odontograma

- **Arcada** — O conjunto dos dentes de um maxilar: a arcada **superior** ou a **inferior**. É também o
  nome do produto.
- **Odontograma** — Desenho da dentição do paciente em que se registra, dente a dente e face a face, a
  **condição** de cada um. O Arcada guarda o odontograma **inicial** e o **atual**, para comparar o antes
  e o depois (quando o inicial é fixado: `<A DEFINIR>`, módulo 3).
- **Notação FDI (ISO 3950)** — Forma de nomear cada dente com **dois dígitos**: o primeiro é o quadrante; o
  segundo, a posição contada a partir da linha média (1 é o incisivo central). `11` é o incisivo central
  superior direito; `36`, o primeiro molar inferior esquerdo. É a notação do Arcada:
  [[ADR-004-notacao-fdi-no-odontograma]]; a regra no código, em [[NotacaoFdi]].
- **Quadrante** — Cada uma das quatro partes em que a boca se divide: a metade direita e a metade
  esquerda de cada arcada. Na ordem da FDI: superior direito, superior esquerdo, inferior esquerdo e
  inferior direito, de 1 a 4 (de 5 a 8 nos decíduos). Direito e esquerdo são sempre **do paciente**. O
  quadrante é o primeiro dígito do número do dente.
- **Dentição permanente** — Os dentes definitivos: até 32, numerados de `11` a `18`, de `21` a `28`, de
  `31` a `38` e de `41` a `48`.
- **Dentição decídua** — Os dentes de leite: até 20, numerados de `51` a `55`, de `61` a `65`, de `71` a
  `75` e de `81` a `85`.
- **Dentição mista** — A fase em que há dentes decíduos e permanentes ao mesmo tempo. O odontograma
  mostra os dois conjuntos.

### Faces

Cada dente tem **cinco faces** no desenho do Arcada: V, M e D, mais **L ou P** (conforme a arcada) e
**O ou I** (conforme o tipo de dente). A regra de quais faces cada dente tem, em [[FacesDoDente]].

| Sigla | Face | Onde fica |
|---|---|---|
| V | vestibular | voltada para o lábio ou para a bochecha |
| L | lingual | voltada para a língua (dentes inferiores) |
| P | palatina | voltada para o palato (dentes superiores) |
| M | mesial | voltada para a linha média, o ponto entre os incisivos centrais |
| D | distal | a oposta da mesial: a mais afastada da linha média |
| O | oclusal | a superfície de mastigação, nos dentes de trás (pré-molares e molares) |
| I | incisal | a borda de corte, nos dentes da frente (incisivos e caninos) |

### Condição

Estado registrado para um dente, ou para uma face dele, no odontograma. A v1 parte de nove condições:
cárie, restauração e selante se marcam numa face; as outras seis valem no dente inteiro. Cada uma tem uma cor na legenda ([[CondicoesELegenda]]) e um **símbolo** só seu, que aparece no dente, na barra de escolha e na legenda, para a cor não ser o único sinal; a tabela dos símbolos está em [[MarcarCondicoes]].

| Condição | O que significa |
|---|---|
| Cárie | lesão de cárie registrada no dente ou na face |
| Restauração | material colocado para reconstituir parte do dente |
| Ausente | dente que não está na boca |
| Extração indicada | dente que o profissional indicou para remover e que ainda está lá |
| Tratamento de canal | dente com tratamento do canal (endodontia) |
| Coroa | peça que recobre a parte visível do dente |
| Implante | pino instalado no osso, no lugar da raiz de um dente ausente, que pode sustentar uma coroa |
| Selante | camada de material que veda os sulcos da face oclusal |
| Fratura | dente quebrado, no todo ou em parte |

## Periodontograma

- **Periodontograma** — Registro do exame da gengiva e do tecido que sustenta os dentes. Cada dente é
  examinado em **seis sítios** (pontos de medida): três pelo lado vestibular e três pelo lado lingual ou
  palatino. O modelo e os cálculos são os do módulo 4; exames podem ser comparados entre si.
- **Profundidade de sondagem** — Distância, em milímetros, da margem da gengiva até onde a sonda
  periodontal para, no fundo do sulco (ou da bolsa). Mede-se em cada sítio.
- **Recessão gengival** — Retração da margem da gengiva, que deixa a raiz exposta. Mede-se, em
  milímetros, da junção entre esmalte e cemento (onde a coroa encontra a raiz) até a margem da gengiva.
- **Nível de inserção clínica** — Distância, em milímetros, da junção entre esmalte e cemento até o fundo
  do sulco: indica quanto suporte o dente ainda tem naquele sítio. Obtém-se somando a recessão à
  profundidade de sondagem; a conta exata do Arcada é a do modelo do módulo 4.
- **Sangramento à sondagem** — Sangramento da gengiva provocado pela sondagem. Anota-se por sítio: houve
  ou não.
- **Supuração** — Saída de pus no sítio examinado. Anota-se por sítio.
- **Mobilidade** — Quanto o dente se move no seu alvéolo (o encaixe no osso) quando testado pelo
  profissional. Registra-se por dente, em graus; a escala é a do modelo do módulo 4 (`<A DEFINIR>`).
- **Lesão de furca** — Perda de tecido de suporte na **furca**, a região onde se dividem as raízes de um
  dente que tem mais de uma (sobretudo os molares). Registra-se em graus; a escala é a do modelo do
  módulo 4 (`<A DEFINIR>`).

## Tratamento, orçamento e dinheiro

- **Procedimento** — Ato clínico que o profissional realiza e cobra: uma restauração, uma limpeza, uma
  extração. No Arcada é um item do catálogo, com nome, especialidade e preço, e entra nos planos de
  tratamento e no atendimento.
- **Especialidade** — Área da odontologia a que um procedimento pertence (endodontia, periodontia e
  ortodontia, por exemplo). O catálogo padrão é organizado por especialidade.
- **Plano de tratamento** — Os procedimentos propostos a um paciente, cada um ligado a um dente e a uma
  face quando cabe, com o valor de cada item. Pode nascer do odontograma e tem uma situação e um
  progresso (quanto já foi realizado). A situação é uma destas: **proposto**, **aprovado**, **em andamento**,
  **concluído** ou **recusado**. O caminho é proposto → aprovado → em andamento → concluído; o proposto também
  pode ser recusado. Concluído e recusado são o fim: não há reabrir plano na v1, e não se pula etapa nem se
  volta ([[SituacaoDoPlano]]).
- **Orçamento** — A proposta de valores do plano de tratamento: preço de cada item, desconto, total e
  forma de pagamento (parcelamento). É impresso para o paciente decidir; **aprovado**, gera as parcelas do
  financeiro.
- **Parcela** — Cada pagamento em que o valor de um orçamento aprovado se divide, com vencimento e valor em
  centavos. A soma das parcelas fecha exatamente o total: o resto da divisão vai para as primeiras
  ([[ADR-005-dinheiro-em-centavos-inteiros]]).
- **Lançamento** — O registro de um valor a receber de um paciente, com vencimento. A parcela de um orçamento
  aprovado é um lançamento: no código os dois são o mesmo tipo (`Lancamento`).
- **Contas a receber** — As parcelas que ainda não receberam baixa, vencidas ou a vencer.
- **Baixa** — O registro de que uma parcela foi paga, com o dia e a forma do pagamento (dinheiro, Pix, cartão
  de débito ou de crédito). Dar baixa tira a parcela das contas a receber; o **estorno de baixa** desfaz o
  registro.
- **Inadimplência** — A situação de uma parcela cujo vencimento passou sem baixa.

## Agenda e atendimento

- **Cadeira** — O posto de atendimento da clínica: a cadeira odontológica com o seu equipamento. A agenda
  é organizada por cadeira, e duas consultas não ocupam a mesma cadeira no mesmo horário.
- **Expediente** — Os horários em que a clínica atende em cada dia da semana. É dentro dele que a agenda
  mostra os horários livres.
- **Consulta** — Um horário marcado na agenda: um paciente, um profissional, uma cadeira, um dia e uma
  hora. Tem uma situação — **agendada**, **confirmada**, **em atendimento**, **concluída**, **faltou** (o
  paciente não veio) ou **cancelada** — e pode ser remarcada ou cancelada, com motivo. **Remarcar** regrava a mesma consulta, sem criar outra, e devolve a **confirmada** a **agendada**: o paciente precisa confirmar o novo horário ([[RemarcarECancelarConsulta]]). O caminho é agendada →
  confirmada → em atendimento → concluída, e a agendada também entra direto em atendimento (quem chega sem
  ter confirmado é atendido do mesmo jeito); da agendada e da confirmada a consulta ainda pode terminar em
  faltou ou cancelada. Concluída, faltou e cancelada são o fim: a consulta não volta nem muda de novo
  ([[2026-09-30-pr-061-situacao-da-consulta]]).
- **Atendimento** — O que se faz quando a consulta acontece: começa **a partir da consulta**, registra os
  procedimentos realizados e a evolução clínica e termina ao ser finalizado. A consulta é o horário; o
  atendimento é o que acontece nele.
- **Evolução clínica** — A anotação, atendimento a atendimento, do que foi feito e observado no paciente.
  Na v1 não tem validade legal de prontuário ([[ADR-001-frontend-primeiro-com-dados-locais]]).
- **Retorno** — A volta do paciente prevista depois de um procedimento, no prazo que a regra daquele
  procedimento define. O Arcada lista os retornos **a vencer** e os **vencidos**; o retorno pode ser
  agendado, adiado ou dispensado.

## Clínica e convênio

- **CRO** — Conselho Regional de Odontologia, o conselho de classe em que o dentista se inscreve. O
  **registro no CRO** é o número dessa inscrição, acompanhado da sigla do estado do conselho: `CRO-SP 12345`
  é `CRO`, hífen, a sigla de uma das 27 UFs, espaço e o número, com os zeros à esquerda. No exemplo estável,
  `CRO-SP 00000`. No Arcada é dado do cadastro do profissional (módulo 5, [[Profissionais]]), e o app só
  confere o **formato** ([[2026-09-30-pr-038-registro-no-cro]]): não consulta o conselho, então registro com
  formato válido não é registro confirmado.
- **Convênio** — Plano odontológico (operadora) que paga, no todo ou em parte, o tratamento do paciente. A
  clínica cadastra os **convênios aceitos** e o paciente pode ter um. O faturamento ao convênio (guias no
  padrão TISS, a Troca de Informações na Saúde Suplementar da ANS) **não** faz parte da v1
  ([[roadmap]]).
- **Particular** — O paciente (ou o atendimento) sem convênio: quem paga é o próprio paciente.

## Produto

- **Módulo** — Cada área funcional do app (pacientes, agenda, financeiro…), que mora em
  `src/modulos/<modulo>/` e se registra sozinha: [[ADR-003-modulos-por-pasta-com-registro-automatico]]. A
  numeração de 0 a 14 é a do [[2026-09-30-plano-da-v1]].
- **Dado de demonstração (semente)** — Pacientes, consultas e demais registros fictícios que acompanham o
  app; nunca vêm de pessoa real. Podem ser restaurados (módulo 14).
