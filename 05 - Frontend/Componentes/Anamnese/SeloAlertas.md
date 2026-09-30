---
tipo: funcionalidade
camada: frontend
area: Anamnese
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, anamnese, alertas, selo]
---

# Selo de alertas da anamnese

## O que é

Uma lista de pílulas com os alertas da anamnese mais recente de um paciente ("Alergia informada: Látex", "Usa
anticoagulante", "Gestante"…). É a peça que a lista e a ficha de pacientes devem mostrar para quem atende ver o
que o paciente informou sem abrir a anamnese (item 2.4 do [[2026-09-30-plano-da-v1]]). Os textos vêm de
`alertasDaAnamnese` ([[2026-09-30-pr-092-anamnese-alertas]]): o selo **só repete o que foi respondido** e não
sugere conduta ([[glossario]], Alerta da anamnese).

## Onde está no código

- `src/modulos/anamnese/SeloAlertas.tsx` — o componente (exportação padrão): `<SeloAlertas pacienteId={id} />`.
- `src/modulos/anamnese/alertas.ts` — as regras e o texto de cada alerta.
- `src/modulos/anamnese/dados.ts` — `anamneses` e `versoesDoPaciente`.
- Usado hoje por `src/modulos/anamnese/FormularioAnamnese.tsx` ([[FormularioAnamnese]]), no topo da aba.

## Comportamento

- **Uma pílula por alerta**, na ordem das regras (alergia primeiro), cada uma com um ícone de aviso decorativo e
  o texto inteiro: o detalhe não é cortado, a pílula quebra de linha. A lista se chama `Alertas da anamnese`
  para leitor de tela.
- **Vale a versão mais recente** do paciente; alertas de versões antigas não contam.
- **Não desenha nada** sem anamnese, sem resposta "sim" a pergunta de alerta ou quando o registro guardado não
  passa em `respostasValidas` (dado estragado): a lista de pacientes não cai por causa de um registro.
- **Selo ausente não quer dizer "sem alergia"**: quem nunca preencheu a anamnese também não tem selo.
- **Reativo**: usa `useColecao`, então aparece e some quando uma versão nova é gravada.
- **Na aba Anamnese** mostra a última versão **salva**; o que está só no rascunho do formulário não entra.
- **Cor**: laranja do tema (`orange-100` e `orange-800`), que acompanha o tema escuro. A cor ajuda; o texto é
  quem diz o alerta.

## Movimento e micro-interações

Nenhuma: a pílula é estática e não é clicável.

## Pendências e limites conhecidos

- **Encaixado no cartão da lista e no cabeçalho da ficha** pelo PR #119 ([[ListaDePacientes]], [[FichaDoPaciente]]); o texto abaixo registra como a decisão foi tomada.
  O módulo de pacientes não foi editado, e o `Modulo` não tem ponto de encaixe para isso. Duas saídas: (1) o
  cartão e o cabeçalho importam `SeloAlertas` de `@/modulos/anamnese/SeloAlertas` e o colocam abaixo do
  telefone; (2) o `Modulo` ganha um campo `seloPaciente` que o `NAVEGACAO` reúne, e pacientes não importa
  anamnese ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- **Pergunta obrigatória nova no questionário** faz as versões antigas deixarem de passar em `respostasValidas`,
  e o selo delas some. O caminho é subir a `versao` da coleção `anamneses` e migrar (`migrar` de `criarColecao`).
- **Custo por cartão**: cada selo filtra e ordena a coleção inteira; desprezível para dezenas de pacientes.

## Histórico de mudanças

- [[2026-09-30-pr-111-anamnese-selo]] — o selo, usado no topo da aba Anamnese; encaixe em pacientes pendente.
