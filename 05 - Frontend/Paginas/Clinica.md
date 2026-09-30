---
tipo: funcionalidade
camada: frontend
area: Clinica
rota: /clinica
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, clinica, cadastro]
---

# Clínica (tela)

## O que é

A tela de cadastro da clínica, no grupo **Cadastros** da coluna lateral. Cada assunto é um cartão, e os
cartões ficam empilhados na mesma página. Hoje há cinco, nesta ordem: os **dados da clínica**, o **expediente** ([[Expediente]]), os **profissionais** ([[Profissionais]]), as **cadeiras** ([[Cadeiras]]) e os **convênios aceitos** ([[Convenios]]).

## Onde está no código

- `src/modulos/clinica/modulo.ts` — registra a rota `/clinica` e o item da coluna (grupo `cadastros`,
  ordem 20, ícone `building`); o registro acha o módulo sozinho
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- `src/modulos/clinica/PaginaClinica.tsx` — a página: `PageShell` e os cartões.
- `src/modulos/clinica/DadosDaClinica.tsx` — o cartão do formulário.
- `src/modulos/clinica/Profissionais.tsx` — o cartão da equipe ([[Profissionais]]).
- `src/modulos/clinica/Cadeiras.tsx` — o cartão dos postos de atendimento ([[Cadeiras]]).
- `src/modulos/clinica/Expediente.tsx` e `expediente.ts` — o cartão do expediente (`ExpedienteDaClinica`) e as
  regras de validar e salvar a semana ([[Expediente]]).
- `src/modulos/clinica/Convenios.tsx` e `convenios.ts` — o cartão dos convênios aceitos e as regras de adicionar
  e remover ([[Convenios]]).
- `src/modulos/clinica/dadosDaClinica.ts` — `validarDadosDaClinica`, `salvarDadosDaClinica`,
  `camposDaClinica` e os `LIMITES` de tamanho.
- Dados: coleção `clinica` (registro único, id `CLINICA_ID`) em `src/dados/colecoes.ts`; tipo `Clinica`
  em `src/dominio/clinica.ts`.

## Comportamento

### Dados da clínica

- **Campos**: nome (obrigatório, até 100 caracteres), telefone (até 20), endereço (até 150), cidade
  (até 60) e UF (lista das 27 siglas, ou vazio). Os dados saem no cabeçalho dos documentos impressos.
- **Salvar** valida pela função de escrita, não pela tela. Com erro, cada campo mostra a sua mensagem,
  aparece «Revise os campos destacados.» e nada é gravado. Sem erro, grava e avisa «Dados da clínica
  salvos».
- **O que é gravado**: os textos aparados; campo opcional vazio fica ausente do registro. O `expediente`
  e o resto do registro não são tocados. Se a coleção estiver vazia, o registro nasce com a semana
  fechada.
- **Telefone** é texto livre: sem máscara e sem checagem de DDD.
- **Abre com o que está salvo.** A clínica é lida ao montar a tela; mudança vinda de outra aba não
  sobrescreve o que está sendo digitado.

### Expediente

Os sete dias da semana, cada um fechado ou com abertura, fechamento e um intervalo (o almoço) opcional. «Salvar
expediente» grava a semana inteira ou nada, e é esse expediente que a agenda lê para sugerir horários livres. O
detalhe está em [[Expediente]].

### Convênios aceitos

Uma lista simples de nomes, com um campo para acrescentar e um botão para remover cada um. Não mexe em pacientes
nem em planos: `Paciente.convenio` é texto livre. O detalhe está em [[Convenios]].

## Movimento e micro-interações

O cartão entra subindo quando aparece (`GlassCard`); ao salvar, um aviso passageiro confirma.

## Histórico de mudanças

- [[2026-09-30-pr-048-dados-da-clinica]] — Dados da clínica: formulário de nome, telefone, endereço e cidade/UF, e a rota `/clinica` com o item da coluna.
- [[2026-09-30-pr-064-profissionais-da-clinica]] — o cartão de profissionais, depois dos dados da clínica.
- [[2026-09-30-pr-069-cadeiras-da-clinica]] — o cartão de cadeiras, depois dos profissionais.
- [[2026-09-30-pr-107-expediente-da-clinica]] — o cartão do expediente, depois dos dados da clínica.
- [[2026-09-30-pr-125-convenios-aceitos-da-clinica]] — o cartão de convênios aceitos, depois das cadeiras.
