---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: /pacientes/novo
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, cadastro, formulario]
---

# Cadastro de paciente

## O que é

O formulário que cadastra um paciente novo. Abre pelo botão `Novo paciente` da [[ListaDePacientes]] e, ao
salvar, leva à ficha do paciente.

## Onde está no código

- `src/modulos/pacientes/modulo.ts` — a rota `/pacientes/novo`.
- `src/modulos/pacientes/CadastroPaciente.tsx` — a tela (cartão de vidro com o formulário).
- `src/modulos/pacientes/FormularioPaciente.tsx` — os campos, as mensagens de erro e o envio.
- `src/modulos/pacientes/regras.ts` — a regra de escrita: `validarPaciente`, `montarPaciente` e
  `cadastrarPaciente`.
- Grava na coleção `pacientes` (`src/dados/colecoes.ts`) por `cadastrarPaciente`.

## Comportamento

| Campo | Regra |
|---|---|
| Nome completo | obrigatório; até 120 caracteres; espaços repetidos viram um só |
| Data de nascimento | obrigatória; data que existe no calendário, de 01/01/1900 até hoje (nascer hoje pode) |
| CPF | opcional; máscara `000.000.000-00` a cada tecla; com valor, os dígitos verificadores têm de estar certos ([[CpfDoPaciente]]); guardado só com os 11 dígitos |
| Telefone | opcional; com valor, DDD + número — o mesmo critério dos botões de contato ([[ContatoRapido]]) |
| E-mail | opcional; formato `usuario@dominio.tld` |
| Convênio | opcional; em branco, grava `Particular` |
| Observações | opcional; texto corrido de até 1000 caracteres |

- **Erros só depois da primeira tentativa de envio.** Daí em diante são recalculados a cada tecla: corrigir o
  campo tira a mensagem dele. O foco vai para o primeiro campo com erro, e um aviso só para leitor de tela
  lê todos os erros de uma vez.
- **A regra de verdade é `regras.ts`.** O formulário mostra as mensagens de `validarPaciente`, mas
  `cadastrarPaciente` valida de novo e lança, sem gravar, se algo estiver errado. A mensagem cita os campos,
  nunca os valores digitados.
- **Salvar** grava o paciente com um id novo e vai para a [[FichaDoPaciente]] (`/pacientes/<id>`).
  **Cancelar** volta à lista sem gravar.
- **Autopreenchimento desligado** (`autoComplete="off"`): o navegador não põe o nome, o telefone e o e-mail
  de quem usa o app no cadastro do paciente. A validação nativa também é desligada (`noValidate`): as
  mensagens são as do app.
- **Fora do escopo**: não impede CPF repetido entre dois pacientes, e telefone estrangeiro não é aceito.

## Movimento e micro-interações

Cartão de vidro que sobe ao entrar; campos do kit com a borda que muda no foco e a vermelha no erro. Sem
animação própria.

## Histórico de mudanças

- [[2026-09-30-pr-071-cadastro-de-paciente]] — a tela, as regras de escrita e o botão `Novo paciente` na lista.
- [[2026-09-30-pr-076-ficha-do-paciente]] — salvar passa a abrir a [[FichaDoPaciente]]; até então a rota não existia.
