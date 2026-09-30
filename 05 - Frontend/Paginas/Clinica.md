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
cartões ficam empilhados na mesma página. Hoje há um: os **dados da clínica**. Profissionais, cadeiras,
expediente e convênios entram depois, como novos cartões do mesmo módulo.

## Onde está no código

- `src/modulos/clinica/modulo.ts` — registra a rota `/clinica` e o item da coluna (grupo `cadastros`,
  ordem 20, ícone `building`); o registro acha o módulo sozinho
  ([[ADR-003-modulos-por-pasta-com-registro-automatico]]).
- `src/modulos/clinica/PaginaClinica.tsx` — a página: `PageShell` e os cartões.
- `src/modulos/clinica/DadosDaClinica.tsx` — o cartão do formulário.
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

## Movimento e micro-interações

O cartão entra subindo quando aparece (`GlassCard`); ao salvar, um aviso passageiro confirma.

## Histórico de mudanças

- [[2026-09-30-pr-048-dados-da-clinica]] — Dados da clínica: formulário de nome, telefone, endereço e cidade/UF, e a rota `/clinica` com o item da coluna.
