---
tipo: indice
ultima_atualizacao: 2026-09-30
tags: [indice, produto]
---

# Visão de produto — Arcada

O **Arcada** é a gestão de um consultório odontológico: pacientes, anamnese, odontograma,
periodontograma, tabela de procedimentos, plano de tratamento e orçamento, agenda por cadeira,
atendimento, financeiro, retornos e documentos. Roda em celular, tablet e computador.

> **Estado (2026-09-30): v1 em construção — só no navegador, com dados fictícios.** Não há backend,
> banco nem login. A decisão está em [[ADR-001-frontend-primeiro-com-dados-locais]]; o que falta fazer,
> em [[2026-09-30-plano-da-v1]].

## Para quem

- **O dentista autônomo**, que faz tudo sozinho: atende, agenda, orça e cobra.
- **A clínica pequena**, de 1 a 5 cadeiras, com **recepção**: quem agenda e cobra não é quem atende, e os
  dois precisam olhar o mesmo dia.

Na v1 não há login nem papéis: quem abre o app vê tudo. O controle de acesso por papel é trabalho de
depois da v1 ([[roadmap]]).

## O problema de hoje

| Como se faz | O que costuma dar errado | Onde o Arcada responde |
|---|---|---|
| Agenda em papel e no WhatsApp | dois pacientes no mesmo horário e na mesma cadeira; ninguém enxerga o dia inteiro num lugar só | Agenda |
| Orçamento em Word | total e parcelas feitos na mão; cada orçamento sai de um jeito | Plano de tratamento e orçamento |
| Odontograma em ficha de papel | a ficha se perde e não dá para comparar o estado de hoje com o de meses atrás | Odontograma e periodontograma |
| Parcela esquecida | a parcela vence e ninguém é lembrado de cobrar | Financeiro |
| Retorno que ninguém lembra | o paciente que deveria voltar não é chamado | Retornos |

## O que a v1 cobre

Quinze módulos (0 a 14), entregues em 108 PRs pequenos. O backlog, item a item, está em
[[2026-09-30-plano-da-v1]]; o significado dos termos, em [[glossario]].

| Módulo | O que a pessoa faz |
|---|---|
| 0 · Fundação | abre o app: casca, sistema visual, dados de demonstração e demo publicada |
| 1 · Pacientes | cadastra, busca e abre a ficha; chama por WhatsApp ou telefone |
| 2 · Anamnese | registra o histórico de saúde e vê os alertas que ele gera |
| 3 · Odontograma | marca a condição de cada dente e de cada face, em notação FDI |
| 4 · Periodontograma | registra o exame de seis sítios por dente e compara exames |
| 5 · Clínica e equipe | cadastra a clínica, os profissionais, as cadeiras, o expediente e os convênios |
| 6 · Procedimentos | mantém o catálogo de procedimentos e os preços |
| 7 · Plano de tratamento e orçamento | monta o plano a partir do odontograma, orça e parcela |
| 8 · Agenda | marca consultas por cadeira e profissional, sem conflito de horário |
| 9 · Atendimento | atende a partir da consulta e registra o que foi feito |
| 10 · Financeiro | recebe as parcelas, dá baixa e vê quem está devendo |
| 11 · Retornos | vê quem deve voltar e entra em contato |
| 12 · Documentos | imprime receituário, atestado e declaração, com o cabeçalho da clínica |
| 13 · Painel | vê o dia e o mês num relance |
| 14 · Sistema | faz backup, busca em tudo e instala como aplicativo |

### Como as peças se ligam

1. O paciente é cadastrado (1) e a anamnese (2) entra na ficha; os alertas aparecem no cartão e na ficha.
2. Nos exames, o odontograma (3) e, quando cabe, o periodontograma (4) registram o estado da boca.
3. Do odontograma nasce o plano de tratamento (7), com procedimentos do catálogo (6). O orçamento é a
   proposta de valores; o paciente aprova ou não.
4. A agenda (8) marca as consultas por cadeira e profissional, dentro do expediente da clínica (5).
5. Na consulta, o atendimento (9) registra o procedimento realizado, e o odontograma se atualiza.
6. O orçamento aprovado vira parcelas no financeiro (10): baixa, caixa e inadimplência.
7. O procedimento pode ter regra de retorno (11): quem deve voltar volta para a agenda.

Documentos (12) imprimem com o cabeçalho da clínica; o painel (13) resume o dia e o mês; o sistema (14)
cuida de backup, busca e instalação. Cada fluxo ganha nota própria em `05 - Frontend/Fluxos/`
([[arcada-frontend]]).

## O que a v1 não cobre

- **Backend e login.** Sem servidor, sem banco, sem conta de usuário, sem papéis: os dados moram no
  navegador de quem abre o app ([[ADR-001-frontend-primeiro-com-dados-locais]]).
- **Prontuário com validade legal.** Não há assinatura digital nem guarda legal do registro. O que o app
  imprime sai com linha para assinatura à mão; a v1 é demonstração, não prontuário.
- **Faturamento de convênio (TISS).** Existe o cadastro dos convênios aceitos e o filtro de pacientes por
  convênio; não existem guias no padrão TISS nem envio à operadora.
- **Estoque.** Nenhum controle de materiais e insumos.
- **Emissão fiscal.** Nenhuma nota fiscal; o recibo do financeiro não substitui documento fiscal.

O que fica para depois está em [[roadmap]].

## Limites que a v1 respeita

- **Dado de saúde é dado pessoal sensível** (LGPD, art. 5º, II). Por isso o app só roda com dado
  fictício: nenhum dado real de pessoa entra em código, semente, teste, commit, print ou nota, e CPF
  nunca vai em semente.
- **O app não sugere conduta clínica.** Os alertas da anamnese só repetem o que foi respondido.
- **Dente se nomeia em notação FDI** ([[ADR-004-notacao-fdi-no-odontograma]]) e **dinheiro é inteiro em
  centavos** ([[ADR-005-dinheiro-em-centavos-inteiros]]).

## Por onde continuar

- [[glossario]] — o significado dos termos do consultório e do produto.
- [[roadmap]] — a v1 e o que vem depois.
- [[2026-09-30-plano-da-v1]] — o backlog de 108 PRs e o fluxo de cada um.
- [[adrs]] — as decisões que moldam o código.
- [[arcada-frontend]] — o mapa das telas e peças.
- [[linguagem-visual]] — o sistema visual.
- [[runbook-rodar-local]] — como rodar o app na máquina.
- [[prs]] — cada mudança, com o porquê.
