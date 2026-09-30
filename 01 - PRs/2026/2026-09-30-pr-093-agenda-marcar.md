---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 93
url: https://github.com/VictorNascimento14/Arcada/pull/93
branch: feat/agenda-marcar
tags: [pr, agenda, marcar-consulta, modal]
status: merged
---

# PR #93 — feat(agenda): marcar consulta bloqueando conflito e feriado

## 🎯 Contexto

Item 8.5 do [[2026-09-30-plano-da-v1]] (módulo 8 · Agenda). Apoia-se na visão do dia (8.3, [[AgendaDoDia]]) e usa as regras puras que já estão na `main`: conflito (8.1), horários livres (8.2) e feriados (8.10). Fecha a issue #84.

## 🔧 Mudanças

- `src/modulos/agenda/marcar.ts` e `marcar.test.ts` — a regra: `validarMarcacao` (campos), `restricoesDaAgenda` (feriado e conflito bloqueiam, ponto facultativo avisa), `horariosSugeridos`, `marcarConsulta` (valida, confere a agenda de novo e grava como `agendada`) e as frases `textoDoBloqueio` e `textoDoAviso`.
- `src/modulos/agenda/MarcarConsulta.tsx` e `MarcarConsulta.test.tsx` — o modal: campos, horários livres em botões, mensagens ao vivo e o botão Marcar travado quando há bloqueio. Só existe montado enquanto aberto: cada abertura começa do zero.
- `src/modulos/agenda/PaginaAgenda.tsx` — o botão **Marcar consulta**; ao marcar, a agenda abre no dia da consulta.
- `src/modulos/agenda/sementes.ts` e `sementes.test.ts` — cada consulta de demonstração traz um procedimento do catálogo padrão (`proc-…`), com o horário igual à duração dele em passos de 15 minutos.

## 🕵️ Dado pessoal (LGPD)

O modal lista nomes de pacientes e a mensagem de conflito cita o paciente da consulta que já estava lá: dado pessoal só na tela, no navegador (ADR-001), sem envio a lugar nenhum. Teste e semente usam nomes fictícios; nenhum CPF aparece.

## 🧠 Decisões técnicas

- **Feriado e conflito bloqueiam; ponto facultativo só avisa.** O bloqueio é o `tipo: "feriado"` de `feriadoDoDia`; o `facultativo` (Carnaval, Corpus Christi) é decisão da clínica. O conflito vem de `conflitosDaConsulta`, então a mensagem diz se é a cadeira, o profissional ou os dois, e qual consulta colidiu.
- **A tela é conforto; `marcarConsulta` é a regra.** O botão trava ao vivo, mas a gravação confere de novo os campos, quem está ativo e a agenda. `src/dados/` ainda não tem escrita própria para consultas, então a regra mora no módulo, no padrão de `salvarCadeira`.
- **Início digitado, horários livres como atalho.** Um campo `type="time"` (passo de 15 min) permite o encaixe, e os botões de horário livre (`horariosLivres`, olhando a cadeira e o profissional já escolhidos; com só um dos dois, só ele conta) tiram a conta de cabeça.
- **Duração de 5 a 480 minutos, sem passar da meia-noite.** Barra o erro de digitação (4500) na fronteira do formulário. Escolher o procedimento preenche a duração dele, que dá para ajustar.
- **Só ativos.** Profissional (`profissionalAtivo`), cadeira (`cadeiraAtiva`) e procedimento (`ativo`) inativos não são oferecidos e são recusados na gravação; a ausência do campo conta como ativo.
- **Sementes com procedimento.** Agora que o catálogo padrão existe, as dez consultas de demonstração o referenciam: a agenda guarda só o id, e sem o catálogo semeado o cartão apenas não mostra o procedimento.

## ⚠️ Armadilhas e aprendizados

- O botão de envio mora no rodapé do `Modal`, fora do `<form>`: `type="submit"` com `form={formId}` (o padrão do modal de cadeiras) o liga ao formulário. Com bloqueio ele fica desabilitado, e `enviar` confere de qualquer jeito (Enter num campo).
- `2026-02-30` passa no formato e volta como `2026-03-02` ao passar por `somarDias`: é assim que `diaValido` reconhece a data que não existe.
- Conferido só no tema escuro, num Chrome headless sobre o `vite preview`, em 1100 e 390 de largura (não há navegador conectado nesta máquina).
- A lista de pacientes do formulário é um `<select>` com todos, sem busca (comentário `ponytail:` no componente).

## 🧪 Como testar

1. `npm run dev`, abra `/agenda` e clique em **Marcar consulta**: o modal abre na data que a agenda mostra, e profissional, cadeira e procedimento inativos ficam fora das listas.
2. Escolha uma cadeira: aparecem os **Horários livres** do dia; clique num deles e o **Início** é preenchido. Escolha um procedimento: a **Duração** vira a prevista dele e os horários se ajustam.
3. Escolha um horário em que a cadeira (ou o profissional) já tem consulta: aparece a mensagem em vermelho com o nome, o horário e o paciente, e o botão **Marcar** fica desabilitado. Um horário que só encosta no fim da outra consulta é aceito.
4. Troque a data para um feriado nacional (12 de outubro): a marcação é barrada. Para o Carnaval de 2026 (17 de fevereiro): só aparece o aviso, e dá para marcar.
5. Preencha tudo e clique em **Marcar**: o modal fecha, a agenda abre no dia da consulta e o cartão aparece na coluna da cadeira, como `Agendada`. Clicar em **Marcar** com o formulário vazio lista o que falta.
6. `npx vitest run --maxWorkers=2 src/modulos/agenda` roda os testes da agenda; `npm run lint && npm run type-check && npm test && npm run build` passam.

## 📎 Documentação afetada

- [[MarcarConsulta]]
- [[AgendaDoDia]]
- [[2026]] (changelog)
