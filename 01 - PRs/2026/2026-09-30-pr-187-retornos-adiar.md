---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 187
url: https://github.com/VictorNascimento14/Arcada/pull/187
branch: feat/retornos-adiar
tags: [pr, retornos, adiar, dispensar]
status: aberto
---

# PR #187 — feat(retornos): adiar ou dispensar o retorno do paciente com o motivo

## 🎯 Contexto

Item 11.4 do [[2026-09-30-plano-da-v1]] (módulo 11 · Retornos): adiar ou dispensar. O glossário diz que o retorno "pode ser agendado, adiado ou dispensado"; o adiar e o dispensar entram aqui, em cima da lista (#176) e do contato (#181). O estado vive na coleção `retornos` do módulo. Fecha a issue #184.

## 🔧 Mudanças

- `dados.ts` — a coleção `retornos` (`EstadoDoRetorno`, um por paciente) e `adiarRetorno`, `dispensarRetorno` e `reativarRetorno`. Quem grava confere: dias inteiros de 1 a 365, motivo obrigatório de até 200 caracteres, o retorno tem de existir, e adiar o dispensado é recusado.
- `estado.ts` — regra pura: `estadoVigente` (o estado vale só para o retorno do mesmo último atendimento), `dataDoRetorno` e `adiadoPara` (a conta do adiamento). Com teste.
- `lista.ts` — `retornosPendentes` aplica o estado (o adiado vale o dia novo, o dispensado sai) e a linha ganha `adiado`; `retornosDispensados` monta o cartão. O parâmetro `intervalos`, que só o teste passava, sai, e o teste passa a usar o procedimento do mapa real.
- `AdiarOuDispensar.tsx` — os dois botões e o campo que se abre na própria linha (dias ou motivo), com o erro no campo e o aviso passageiro. `Dispensados.tsx` — o cartão com o motivo e o **Reativar**. `ListaDeRetornos.tsx` monta os dois.
- Testes: `estado.test.ts`, `dados.test.ts`, `lista.test.ts` (estado e dispensados) e `ListaDeRetornos.test.tsx` (adiar, erro, dispensar, reativar).

## 🕵️ Dado pessoal (LGPD)

O motivo da dispensa é texto livre digitado por quem atende, e o cartão Dispensados o mostra ao lado do nome do paciente. O campo avisa para não pôr dado de saúde e limita a 200 caracteres; o app não confere nem sugere motivo. Fica só no navegador, como todo dado da v1.

## 🧠 Decisões técnicas

- **O estado guarda o último atendimento de que o retorno partiu** e só vale para esse retorno. Uma marca no paciente esconderia, para sempre, o retorno novo de quem foi dispensado em março e voltou a ser atendido em agosto.
- **Adiar conta da data que o retorno tem agora ou de hoje, a que for maior.** Somar ao dia já vencido o deixaria vencido; somar a hoje o que vence em 20 dias o adiantaria.
- **Dispensar exige o motivo e o mostra**: o cartão **Dispensados** lista quem saiu, com o dia e o motivo, e o **Reativar** desfaz. Sem ele a dispensa seria irreversível e o motivo, um dado que ninguém lê.
- **Um estado por paciente** (o `id` é o do paciente): adiar e dispensar são estados do mesmo retorno, e o último grava por cima. O histórico de quem mexeu fica para quando houver backend.
- **A escrita confere contra os dados vivos**: recalcula o retorno na hora de gravar e recusa quem não tem um, em vez de confiar no que a tela mostrava.
- **Formulário na própria linha, sem modal**: a linha continua à vista e não há portal a montar (nada `fixed` dentro de `.glass`).
- **`retornosPendentes` perdeu o parâmetro `intervalos`** e ganhou `estados`: a tela nunca passou o mapa, que era só para o teste.

## ⚠️ Armadilhas e aprendizados

- O adiado para além de 30 dias some da lista até entrar na janela, e não há cartão de adiados (há um `ponytail:` em `lista.ts`). Quem adia 90 dias não vê o paciente por 60: a data sai no aviso passageiro (`até 15/01/2027`).

## 🧪 Como testar

1. `npm run dev`, em **Retornos**: em Rafael Teixeira (vencido há 45 dias) clique **Adiar**, deixe 15 e **Confirmar**. Ele passa para **A vencer nos próximos 30 dias**, com `Vence em 15 dias` e `retorno adiado para …`.
2. Em Beatriz Campos, **Dispensar** e **Confirmar** sem motivo mostra `Diga o motivo da dispensa.`; com `Mudou de cidade` ela sai dos cartões e aparece em **Dispensados**, com o dia e o motivo. **Reativar** a devolve a **Vencidos**.
3. Recarregue a página: o adiado e o dispensado continuam, porque o estado fica no navegador.
4. `npx vitest run src/modulos/retornos --maxWorkers=2`: a regra do adiamento e do estado vigente, a escrita validada, a lista com o estado e a tela (adiar, erro do campo, dispensar, reativar).
5. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build`.

## 📎 Documentação afetada

- [[AdiarEDispensarRetorno]]
- [[ListaDeRetornos]]
- [[ContatoDoRetorno]]
- [[glossario]]
- [[2026-09-30-adiar-e-dispensar-valem-so-para-o-retorno-em-curso]]
- [[2026]] (changelog)
