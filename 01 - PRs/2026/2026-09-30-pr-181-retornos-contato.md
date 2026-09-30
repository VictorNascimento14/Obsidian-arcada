---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 181
url: https://github.com/VictorNascimento14/Arcada/pull/181
branch: feat/retornos-contato
tags: [pr, retornos, whatsapp, contato]
status: aberto
---

# PR #181 — feat(retornos): contatar o paciente pelo WhatsApp e oferecer o atalho para marcar a consulta

## 🎯 Contexto

Item 11.3 do [[2026-09-30-plano-da-v1]] (módulo 11 · Retornos): contato e agendamento do retorno, em cima da lista do #176 (11.2). Segue o desenho da confirmação pelo WhatsApp da agenda (#153), que também usa o `linkWhatsApp` do módulo de pacientes. Fecha a issue #179.

## 🔧 Mudanças

- `src/modulos/retornos/mensagem.ts` — `mensagemDeRetorno` (só o primeiro nome, sem dado de saúde) e `linkDeRetorno`, sobre o `linkWhatsApp` de `pacientes/contato.ts`: `null` quando o telefone não serve. Regra pura, com teste.
- `ContatoDoRetorno.tsx` — o link **WhatsApp** (só com telefone que serve; abre em outra aba) e o atalho **Marcar consulta** para `/agenda`, com o nome do paciente só para leitor de tela.
- `ListaDeRetornos.tsx` — uma linha de contato (`role="group"`) abaixo de cada paciente, nos dois cartões. Teste de tela novo.

## 🕵️ Dado pessoal (LGPD)

A mensagem leva só o primeiro nome do paciente: sem procedimento, data de atendimento, prazo nem outro dado de saúde (o teste confere que não há dígito nela). O telefone vai só na URL do `wa.me`, que abre o WhatsApp de quem usa o app; nada é enviado por servidor. Nas sementes o telefone tem DDD 00 e o link não aparece.

## 🧠 Decisões técnicas

- **Uma mensagem só, para o vencido e para o a vencer**, sem data nem prazo: quem chama diz o mesmo a qualquer paciente, e o que o paciente fez na clínica fica fora do texto.
- **Só o primeiro nome**, como na confirmação da agenda: o sobrenome não vai na mensagem.
- **O WhatsApp some quando o telefone não serve**, em vez de aparecer quebrado: um link com número errado abriria a conversa com a pessoa errada.
- **Marcar consulta só abre a agenda**: ela não lê parâmetro da URL, e o módulo da agenda não muda neste PR. Quem marca escolhe o paciente lá.
- **O nome do paciente vai num `sr-only` depois do texto visível**: a lista repete os dois links em toda linha, e um "Marcar consulta" solto não diz de quem é. Como o texto visível vem primeiro, o nome acessível o contém.

## ⚠️ Armadilhas e aprendizados

- O nome acessível de `WhatsApp` mais `<span class="sr-only"> de Fulano</span>` sai `WhatsAppde Fulano` na conta do Testing Library, que apara o texto de cada elemento interno. O espaço vai num `{" "}` fora do `span`: fica certo no teste e no navegador.

## 🧪 Como testar

1. `npm run dev` e abra **Retornos**: cada paciente tem o link **Marcar consulta**, que leva à agenda. Nas sementes o telefone tem DDD 00, então o **WhatsApp** não aparece (de propósito).
2. Para ver o WhatsApp, dê um telefone com DDD real a um paciente da lista. O app ainda não edita paciente, então pelo console do navegador: `const k='arcada:pacientes',e=JSON.parse(localStorage.getItem(k));e.itens=e.itens.map(p=>p.id==='pac-ret-1'?{...p,telefone:'(11) 91234-5678'}:p);localStorage.setItem(k,JSON.stringify(e));location.reload()`. A linha de Rafael Teixeira ganha o **WhatsApp**, que abre `https://wa.me/5511912345678?text=Olá, Rafael! Está na hora do seu retorno…` (só o primeiro nome).
3. `npx vitest run src/modulos/retornos --maxWorkers=2`: a mensagem (primeiro nome, nenhum dígito, URL, telefones que não servem) e a lista (o WhatsApp só para quem tem telefone, o atalho para todos).
4. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[ContatoDoRetorno]]
- [[ListaDeRetornos]]
- [[ConfirmacaoPeloWhatsApp]]
- [[glossario]]
- [[2026]] (changelog)
