---
tipo: funcionalidade
camada: frontend
area: Pacientes
rota: —
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, pacientes, contato, whatsapp]
---

# Contato rápido

## O que é

Os links para falar com o paciente: WhatsApp e ligação. Os botões `WhatsApp` e `Ligar` estão no cabeçalho da
[[Ficha do paciente]], e o [[Cadastro de paciente]] usa `linkTelefone` como critério de telefone válido.

## Onde está no código

- `src/modulos/pacientes/contato.ts` — `linkWhatsApp` e `linkTelefone`.
- `src/modulos/pacientes/contato.test.ts` — os testes.

## Comportamento

- **Entrada**: o telefone como o usuário digitou — máscara, espaços, `+55`, zero à frente do DDD.
- **Só número brasileiro**: DDD + 8 ou 9 dígitos, ou seja, 10 ou 11 dígitos sem o `55`. O `55` só é
  tirado como código do país quando sobra um número inteiro depois dele; `(55) 91234-5678` tem DDD 55.
- **`linkWhatsApp(telefone, texto?)`**: `https://wa.me/55<DDD><número>`, com `?text=` e o texto
  codificado quando `texto` vem preenchido.
- **`linkTelefone(telefone)`**: `tel:+55<DDD><número>`, em formato internacional.
- **Telefone que não serve** (vazio, curto, longo, estrangeiro): os dois devolvem `null`, e o botão
  correspondente some da ficha; um link com número errado abriria a conversa com a pessoa errada.
- **Só o tamanho é conferido**: DDD e prefixo do número inexistentes passam.

## Histórico de mudanças

- [[2026-09-30-pr-036-pacientes-contato]] — `linkWhatsApp`, `linkTelefone` e os testes; os botões na ficha ficam para depois.
- [[2026-09-30-pr-071-cadastro-de-paciente]] — o cadastro passa a usar `linkTelefone` como critério de telefone válido.
- [[2026-09-30-pr-076-ficha-do-paciente]] — os botões `WhatsApp` e `Ligar` no cabeçalho da ficha.
