---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 76
url: https://github.com/VictorNascimento14/Arcada/pull/76
branch: feat/pacientes-ficha
tags: [pr, pacientes, ficha, abas]
status: merged
---

# PR #76 — feat(pacientes): abrir a ficha do paciente com cabeçalho e abas

## 🎯 Contexto

Item 1.5 do [[2026-09-30-plano-da-v1]] (módulo 1 · Pacientes): a ficha com abas. Lê `NAVEGACAO.abasPaciente` (registro de módulos, item 0.10) e usa `linkWhatsApp`/`linkTelefone` (item 1.7), a idade (item 1.4) e o cadastro (item 1.3); é para onde a lista (1.1) e o cadastro levam. Fecha a issue #58.

## 🔧 Mudanças

- `src/modulos/pacientes/FichaPaciente.tsx` — a tela: cabeçalho com `Avatar`, nome, idade · convênio, telefone e os botões de contato; abas `tablist`/`tab`/`tabpanel` com teclado; mensagem de paciente não encontrado.
- `src/modulos/pacientes/DadosDoPaciente.tsx` — a aba `Dados`: o cadastro em lista de definição, com `Não informado` no que falta.
- `src/modulos/pacientes/exibicao.ts` — `dataBR` (`AAAA-MM-DD` para `DD/MM/AAAA`, sem `Date`), com teste.
- `src/modulos/pacientes/modulo.ts` — a rota `/pacientes/:id` (a estática `/pacientes/novo` tem precedência).
- `src/modulos/pacientes/FichaPaciente.test.tsx` — cabeçalho, botões de contato (com e sem telefone válido), ordem das abas, troca por clique, teclado e paciente inexistente; o registro de módulos é simulado para as abas dos outros módulos não mudarem o teste.
- `src/modulos/pacientes/CadastroPaciente.test.tsx` — o teste de salvar passa a conferir que a ficha abre.

## 🕵️ Dado pessoal (LGPD)

A ficha mostra na tela nome, idade, telefone, e-mail, CPF (com máscara, se cadastrado) e observações; nada sai do navegador. O único caminho para fora é o clique do usuário em `WhatsApp` (`wa.me` em outra aba, com `noopener noreferrer`) ou em `Ligar` (`tel:`): o número só vai ao WhatsApp quando alguém pede. Semente e teste continuam sem CPF e com telefone fictício (DDD `00` na semente: na demo os botões não aparecem).

## 🧠 Decisões técnicas

- **As abas dos outros módulos são lidas de `NAVEGACAO.abasPaciente` dentro do componente**, não no topo do arquivo: `@/modulos` importa o `modulo.ts` do módulo, que importa esta tela, e ler no topo pegaria o registro ainda por montar. A lista já vem em ordem (`ordem`, depois `chave`) e a `Dados` é sempre a primeira.
- **Padrão WAI-ARIA de abas com ativação automática**: `role="tablist"`/`tab`/`tabpanel`, `aria-selected`, `aria-controls` na aba selecionada, `tabindex` 0 só nela e -1 nas outras; ←/→ movem seleção e foco e dão a volta, Home e End vão às pontas. Só a aba ativa é montada: as dos outros módulos não gastam nada até serem abertas.
- **A aba ativa é estado da tela, não da URL.** Um link direto para uma aba (`?aba=`) pode vir quando algum módulo precisar dele.
- **Botões de contato são `<a>`**, com a cara do `Button` secundário (o kit não tem botão-link): `WhatsApp` abre em outra aba (`rel="noopener noreferrer"`, com um aviso só para leitor de tela) e `Ligar` usa `tel:`. Somem quando `linkWhatsApp`/`linkTelefone` devolvem `null`.
- **A `Dados` tem a mesma assinatura das outras abas** (`{ pacienteId }`): a ficha renderiza todas do mesmo jeito e a `Dados` não é caso especial.
- **Idade ilegível não derruba a ficha**: sem idade, o cabeçalho mostra só o convênio (a mesma regra da lista).

## ⚠️ Armadilhas e aprendizados

- **Import circular controlado**: `FichaPaciente` importa `@/modulos`, que importa `pacientes/modulo.ts`, que importa `FichaPaciente`. Funciona porque a ficha só lê `NAVEGACAO` ao renderizar e porque o componente é `export default function` (declaração içada); `const Ficha = () => …` daria erro de inicialização quando o `modulo.ts` fosse avaliado primeiro.
- **O anel de foco das abas era cortado**: `overflow-x-auto` corta também na vertical, e o foco do kit é um `outline` de 2px com 2px de respiro. O contêiner das abas tem `p-1` (e `-mx-1`) por isso.
- **O teste da ficha simula `@/modulos`**: com o registro de verdade, cada módulo que ganhasse uma aba mudaria a lista esperada e quebraria o teste sem culpa da ficha. A fábrica do `vi.mock` usa `createElement`, sem JSX.

## 🧪 Como testar

1. `npm run dev` e, em `/pacientes`, abra um cartão: a ficha mostra nome, idade, convênio e telefone, e a aba `Dados` com nascimento, CPF, telefone, e-mail, convênio e observações (`Não informado` no que faltou).
2. Cadastre um paciente com telefone de DDD válido (por exemplo, `(11) 90000-0002`): a ficha mostra `WhatsApp` (passe o mouse: o endereço é `wa.me/5511900000002`; não precisa abrir) e `Ligar` (`tel:+5511900000002`). Nos pacientes de demonstração, de DDD `00`, os dois botões não aparecem.
3. Com o foco numa aba, use ←/→, Home e End: a seleção e o foco andam juntos e dão a volta nas pontas. Hoje só a `Dados` existe; as outras aparecem quando cada módulo registrar a sua.
4. Abra `/pacientes/qualquer-coisa`: aparece `Paciente não encontrado`, com o link `Voltar à lista de pacientes`.
5. `npm run lint && npm run type-check && npx vitest run --maxWorkers=2 && npm run build` passam.

## 📎 Documentação afetada

- [[Ficha do paciente]]
- [[Lista de pacientes]]
- [[Cadastro de paciente]]
- [[ContatoRapido]]
- [[IdadeEFaixaEtaria]]
- [[2026]] (changelog)
