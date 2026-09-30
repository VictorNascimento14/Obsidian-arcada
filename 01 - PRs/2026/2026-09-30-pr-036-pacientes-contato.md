---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 36
url: https://github.com/VictorNascimento14/Arcada/pull/36
branch: feat/pacientes-contato
tags: [pr, pacientes, contato, whatsapp]
status: aberto
---

# PR #36 — feat(pacientes): gerar os links de WhatsApp e de ligação do paciente

## 🎯 Contexto

Módulo 1 (Pacientes) do [[2026-09-30-plano-da-v1]], item 1.7: contato rápido pelo WhatsApp e telefone. Só a parte pura; os botões na ficha ficam para depois porque a ficha ainda não existe. Fecha a issue #28.

## 🔧 Mudanças

- `src/modulos/pacientes/contato.ts` — `linkWhatsApp` e `linkTelefone`, sobre uma normalização única do telefone.
- `src/modulos/pacientes/contato.test.ts` — as formas de digitar o mesmo número, o fixo, o DDD 55, o texto codificado e os telefones recusados.

## 🕵️ Dado pessoal (LGPD)

Telefone é dado pessoal. Os testes usam números de sequência óbvia, sem ligação com pessoa, e o nome `Paciente Exemplo`. Telefone que não serve não vira link: um botão desabilitado é melhor que uma conversa aberta com o número errado.

## 🧠 Decisões técnicas

- Devolve `null` quando o telefone não serve, em vez de um link quebrado: quem monta o botão decide desabilitá-lo. Telefone é campo opcional no cadastro, então o vazio também cai aqui.
- Só número brasileiro, com 10 ou 11 dígitos sem o `55`. O `55` só é tirado como código do país quando sobra um número inteiro depois dele (12 dígitos ou mais); com 10 ou 11 dígitos, o `55` do começo é o DDD 55 e o código do país é acrescentado.
- O zero à frente é descartado (`(011) 91234-5678`): nenhum DDD começa com zero. O código do país é tirado antes, para `+55 (011) …` também funcionar.
- `tel:` em formato internacional (`tel:+55…`), que não depende do país configurado no aparelho.
- O texto vai por `encodeURIComponent`; sem texto, sem `?text=`.
- `ponytail:` confere só o tamanho do número. Teto conhecido: número com o tamanho certo e DDD ou prefixo inexistente vira link; validar isso pede a lista de DDDs e o plano de numeração.

## ⚠️ Armadilhas e aprendizados

- Tirar o `55` só porque o número começa com `55` erra o DDD 55; quem decide é o tamanho.
- Sem codificar, um `&` ou um `#` no texto trunca a mensagem; o teste confere que o texto volta inteiro pelo `URL`.

## 🧪 Como testar

1. `npm test -- src/modulos/pacientes` roda `contato.test.ts`: telefone com máscara, com `+55` e com zero à frente; fixo; DDD 55; texto codificado; telefones que não servem.
2. `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[ContatoRapido]]
- [[2026]] (changelog)
