---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 156
url: https://github.com/VictorNascimento14/Arcada/pull/156
branch: feat/perio-sangramento
tags: [pr, periodontograma, sangramento, supuracao]
status: merged
---

# PR #156 — feat(periodontograma): marcar sangramento e supuração em cada sítio

## 🎯 Contexto

Item 4.4 (Sangramento e supuração) do [[2026-09-30-plano-da-v1]]. Estende a grade dos itens 4.2 e 4.3 (PRs #138 e #146): a marca é mais um grupo de células com `data-celula`, então o teclado do item 4.3 chega a ela sem mudança. Fecha a issue #150.

## 🔧 Mudanças

- `src/modulos/periodontograma/exame.ts` (+ teste): `MedidaSitio.supuracao?: boolean`, campo novo e opcional; o teste garante que a supuração não muda nenhum índice.
- `src/modulos/periodontograma/grade.ts`: `Sinal` (`sangramento` ou `supuracao`) e `ROTULOS_DE_SINAL`.
- `src/modulos/periodontograma/dados.ts` (+ testes): `alternarSinal(pacienteId, dente, sitio, sinal)`, que liga (guarda `true`) ou desliga (tira o campo) o sinal no exame de hoje.
- `src/modulos/periodontograma/GradeDeSondagem.tsx`: as colunas Sangramento e Supuração, uma marca por sítio (`aoAlternar`).
- `src/modulos/periodontograma/AbaPeriodonto.tsx` (+ testes): liga a grade a `alternarSinal`, e o texto do cabeçalho explica as marcas.

## 🧠 Decisões técnicas

- **A marca é um botão de alternância** (`aria-pressed`), não uma caixa de seleção: um círculo de 24 px por sítio mantém a grade em cerca de 890 px, que cabe na ficha em 1440 px sem rolagem, e o botão traz Enter e Espaço nativos. O nome para leitor de tela segue o dos campos: `Sangramento, dente 16, mesiovestibular`.
- **O estado não depende só da cor**: desligada é um círculo vazio; ligada, cheio (vermelho no sangramento e laranja na supuração), e `aria-pressed` diz o estado ao leitor de tela.
- **Desligar tira o campo em vez de guardar `false`**: desmarcado e sem sinal são a mesma coisa, e o exame fica sem ruído. Um `sangramento: false` que venha guardado continua valendo como sem sangramento, porque o cálculo só olha o verdadeiro.
- **`alternarSinal` reaproveita o `atualizarSitio` de `registrarMedida`**: a mesma criação do exame do dia e a mesma conferência de paciente e dente.
- **A supuração não entra em índice**: o glossário só pede que se anote por sítio, nenhum dos quatro índices do item 4.1 a usa, e um teste garante que ela não muda nenhum.
- **O teclado não mudou**: as marcas têm `data-celula`, e o passo vertical do `navegar` é lido do tamanho da linha, que agora tem 24 células, como o item 4.3 previu.

## ⚠️ Armadilhas e aprendizados

- Sangramento marcado num sítio sem profundidade não entra no percentual: o modelo deixa de fora o sítio ainda não medido, para o percentual nunca passar de 100. Na tela a marca fica ligada, e o número só a contará quando a profundidade existir.
- Vermelho e laranja ficam em grupos separados, cada um com cabeçalho, então o significado nunca depende de distinguir as duas cores.
- A nota [[GradeDeSondagem]] descreve a grade só com profundidade e margem e diz, em "Limites conhecidos", coisas que este PR e o #146 mudaram; a atualização fica para o orquestrador, em lote (já apontada no PR #146).

## 🧪 Como testar

1. `npx vitest run --maxWorkers=2 src/modulos/periodontograma` — `dados.test.ts` (`alternarSinal`), `exame.test.ts` (a supuração não muda índice) e `AbaPeriodonto.test.tsx` (as marcas e o teclado).
2. `npm run dev`, aba **Periodonto**: cada arcada tem, depois da margem, as colunas **Sangramento** e **Supuração**, com uma marca por sítio.
3. Clicar numa marca de sangramento: fica vermelha. Clicar na de supuração do mesmo sítio: fica laranja, sem mexer na outra. Clicar de novo desliga.
4. Recarregar a página: as marcas continuam (chave `arcada:exames-perio`, campos `sangramento` e `supuracao` do sítio).
5. Com o foco na última margem de um dente, a seta para a direita vai à primeira marca de sangramento; Enter ou Espaço liga e desliga a marca focada.

## 📎 Documentação afetada

- [[SangramentoESupuracao]]
- [[GradeDeSondagem]]
- [[glossario]]
- [[2026]] (changelog)
