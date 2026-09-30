---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: /pacientes/:id
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, ficha, abas]
---

# Ficha do paciente

## O que é

A tela de um paciente: quem é (nome, idade, convênio, telefone), como falar com ele e, em abas, tudo o que
cada módulo guarda sobre ele. A [[ListaDePacientes]] e o [[CadastroDePaciente]] levam a ela.

## Onde está no código

- `src/modulos/pacientes/modulo.ts` — a rota `/pacientes/:id`.
- `src/modulos/pacientes/FichaPaciente.tsx` — o cabeçalho com o selo de alertas, as abas, o teclado e a rolagem até a aba ativa.
- `src/modulos/pacientes/DadosDoPaciente.tsx` — a aba `Dados`.
- `src/modulos/pacientes/exibicao.ts` — idade, convênio e data em texto.
- `NAVEGACAO.abasPaciente` (`@/modulos`) — as abas dos outros módulos.

## Comportamento

- **Cabeçalho**: avatar com as iniciais, nome, `idade · convênio` (sem convênio, `Particular`) e o telefone.
  A idade vem de [[IdadeEFaixaEtaria]]; nascimento ilegível omite a idade em vez de quebrar a tela.
- **Alertas**: abaixo do nome e do telefone, o selo de alertas da anamnese mais recente ([[SeloAlertas]]): uma
  pílula por alerta, como `Alergia informada: …`. Paciente sem alerta não ganha espaço vazio no cabeçalho.
- **Contato** ([[ContatoRapido]]): `WhatsApp` (abre `wa.me` em outra aba) e `Ligar` (`tel:`). Cada botão some
  quando o telefone não serve para ele: em branco, sem DDD ou fora do padrão brasileiro. Na demo, os
  pacientes de semente têm DDD `00` de propósito, então os botões não aparecem para eles; aparecem para
  paciente cadastrado com telefone de DDD válido.
- **Abas**: `Dados` é sempre a primeira; depois vêm as que os módulos registram em `abaPaciente` no
  `modulo.ts`, por `ordem` (no empate, pela `chave`). Cada uma recebe o `pacienteId`. Só a aba ativa é
  montada. Uma delas é `Tratamentos` (ordem 40), do módulo de tratamentos ([[PlanoDeTratamento]]).
- **Aba `Dados`**: nascimento (`DD/MM/AAAA`), CPF (com máscara), telefone, e-mail, convênio e observações; o
  que não foi preenchido aparece como `Não informado`. As observações mantêm as quebras de linha.
- **Teclado** (padrão WAI-ARIA de abas): ← e → trocam de aba e movem o foco, dando a volta nas pontas; Home
  vai à primeira e End à última. Só a aba selecionada entra na sequência do Tab. Ao trocar de aba, a faixa rola até a aba ativa (`scrollIntoView`, suave, ou sem animação com `prefers-reduced-motion`): no celular, a aba escolhida — ou a que o teclado alcançou — aparece inteira, sem a página pular na vertical.
- **A aba ativa não vai na URL**: é estado da tela, e recarregar volta à `Dados`.
- **Paciente inexistente** (`/pacientes/qualquer-coisa`): `Paciente não encontrado` e o link
  `Voltar à lista de pacientes`.

## Como um módulo põe a sua aba

No `modulo.ts` do módulo: `abaPaciente: { ordem, rotulo, Componente }`, em que `Componente` recebe
`{ pacienteId }`. A ficha não muda.

## Movimento e micro-interações

Cartões de vidro que sobem ao entrar; a aba selecionada é uma pílula verde escura e as outras mudam de cor
no hover. A faixa de abas rola na horizontal no celular e, a cada troca, leva a aba ativa para a vista.

## Histórico de mudanças

- [[2026-09-30-pr-076-ficha-do-paciente]] — a ficha com a aba `Dados`, os botões de contato e o teclado das abas.
- [[2026-09-30-pr-097-tratamentos-itens]] — a aba `Tratamentos` entra pelo registro de abas, sem editar a ficha.
- [[2026-09-30-pr-119-alertas-do-paciente]] — o selo de alertas da anamnese abaixo do nome e do telefone.
- [[2026-09-30-pr-173-ficha-aba-ativa]] — a faixa de abas rola até a aba ativa.
