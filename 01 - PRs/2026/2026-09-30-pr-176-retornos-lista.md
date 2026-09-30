---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 176
url: https://github.com/VictorNascimento14/Arcada/pull/176
branch: feat/retornos-lista
tags: [pr, retornos, lista, vencidos]
status: aberto
---

# PR #176 — feat(retornos): listar os retornos vencidos e os dos próximos 30 dias

## 🎯 Contexto

Item 11.2 do [[2026-09-30-plano-da-v1]] (módulo 11 · Retornos): a lista de retornos a vencer e vencidos, sobre a regra do #165 (11.1). O contato (11.3) e o adiar ou dispensar (11.4) entram em cima desta tela. Fecha a issue #168.

## 🔧 Mudanças

- `src/modulos/retornos/lista.ts` — `retornosPendentes(pacientes, consultas, planos, hoje)`: o retorno de cada paciente atendido que já venceu ou vence em até 30 dias (`JANELA_A_VENCER_DIAS`), com a situação `vencido` ou `a-vencer` e os dias; do prazo mais antigo ao mais próximo, nome no desempate. Regra pura, com teste.
- `ListaDeRetornos.tsx` e `PaginaRetornos.tsx` — dois cartões, Vencidos e A vencer nos próximos 30 dias, com avatar, nome (link para a ficha), as duas datas e a pílula do prazo. Teste de tela cobre os dois cartões, o singular e os estados vazios.
- `modulo.ts` — a rota `/retornos` e o item da coluna (Consultório, ordem 30, ícone `refresh`), sem aba na ficha.
- `sementes.ts` — quatro pacientes de exemplo com uma consulta concluída antiga cada, datada a partir de hoje para o retorno cair em −45, −12, +9 e +24 dias. Roda depois da agenda.
- `regra.test.ts` — meses negativos em `somarMeses`, que as sementes usam.

## 🕵️ Dado pessoal (LGPD)

A lista mostra o nome do paciente e as datas do último atendimento e do retorno; não mostra procedimento, anamnese nem outro dado de saúde. As sementes usam nomes de exemplo e telefone com DDD 00 (nenhum número real), sem CPF.

## 🧠 Decisões técnicas

- **Quem nunca foi atendido não aparece, e o paciente que saiu do cadastro também não**: sem atendimento não há de onde contar, e sem cadastro não há a quem chamar. (A inadimplência mantém o órfão porque ali há dinheiro devido.)
- **O retorno de hoje é a vencer, com 0 dia**, como a parcela que vence hoje: ainda não é atraso. A janela de 30 dias inclui o 30º dia.
- **Dois cartões, um por grupo**, em vez de uma lista com filtro: cada grupo tem a sua urgência, e o estado vazio de cada um diz a coisa certa.
- **A lista não mostra o procedimento**: é dado de saúde, e quem chama o paciente não precisa dele.
- **Sementes com pacientes novos**: os oito do núcleo têm consulta nesta semana, e a agenda conclui as dos dias que já passaram, então o retorno deles só vence daqui a meses — e, conforme o dia da semana em que o app abre, nenhum sobraria. Quatro pacientes de exemplo fora da agenda deixam a lista igual em qualquer dia (o teste cobre seis datas, de segunda a domingo e viradas de mês).
- **Paciente com consulta marcada continua na lista** até ser atendido: a regra olha o que já foi feito. Um `ponytail:` em `lista.ts` nomeia o caminho se isso incomodar.

## ⚠️ Armadilhas e aprendizados

- O semeador dos retornos **acrescenta** à coleção das consultas, ao contrário dos outros, que só plantam em coleção vazia. Se rodasse antes da agenda, ela pularia as consultas dela: por isso ele só semeia com a coleção já preenchida (o registro acha os semeadores em ordem alfabética, e `agenda` vem antes de `retornos`).
- `somarMeses` com meses negativos não é o inverso exato do positivo em fim de mês (31/10 menos 1 mês é 30/09, mas 30/09 mais 1 mês é 30/10). As sementes contam com margem de dias, não com a data exata.

## 🧪 Como testar

1. `npm run dev` e abra **Retornos** na coluna (Consultório, depois de Pacientes): **Vencidos** traz Rafael Teixeira (45 dias) e Beatriz Campos (12 dias); **A vencer nos próximos 30 dias** traz Otávio Ramos (9 dias) e Sara Nogueira (24 dias), cada um com `Último atendimento em … · retorno previsto em …`.
2. Clique no nome de um paciente: abre a ficha dele.
3. `npx vitest run src/modulos/retornos --maxWorkers=2`: a regra da lista (hoje na borda, o 30º dia incluído e o 31º fora, quem nunca foi atendido, a ordem), a tela e as sementes em seis datas.
4. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[ListaDeRetornos]]
- [[glossario]]
- [[2026-09-30-retorno-conta-do-ultimo-atendimento]]
- [[2026]] (changelog)
