---
tipo: funcionalidade
camada: frontend
area: Documentos
rota: /documentos
ultima_atualizacao: 2026-09-30
tags: [funcionalidade, documentos, receituario, impressao]
---

# Receituário

## O que é

O primeiro documento da tela **Documentos** (`/documentos`, item da coluna no grupo Gestão): um texto livre, com
paciente, profissional e data, que se imprime numa folha com o cabeçalho da clínica e a linha para assinar à mão
(item 12.2 do [[2026-09-30-plano-da-v1]]). A v1 é demonstração, não prontuário: não há assinatura digital nem
validade jurídica, e **o app não sugere medicamento, dose nem conduta**: o texto é todo de quem escreve.

## Onde está no código

- `src/modulos/documentos/modulo.ts` — registra a rota `/documentos` e o item da coluna (grupo `gestao`, ordem 30,
  ícone `file`). Sem aba na ficha do paciente.
- `src/modulos/documentos/PaginaDocumentos.tsx` — a tela: o aviso de demonstração e um cartão por documento.
- `src/modulos/documentos/Receituario.tsx` — o cartão do receituário e a folha, desenhada só durante a impressão.
- `src/modulos/documentos/receituario.ts` — a regra pura: `camposDoReceituario`, `prepararReceituario`,
  `LIMITE_DO_TEXTO`.
- `src/modulos/documentos/validacao.ts` — `resolverEscolha` (paciente e profissional ativo) e `dataValida`,
  compartilhadas pelos documentos.
- `src/modulos/documentos/PacienteEProfissional.tsx`, `Selecao.tsx` — as duas escolhas de todo documento e o
  `<select>` do sistema (a mesma caixa do de [[MarcarConsulta]]).
- `src/componentes/FolhaImpressa.tsx`, `src/componentes/useImpressao.ts` — a folha e o mecanismo de impressão
  ([[FolhaImpressa]]).

## Comportamento

- **Campos**: `Paciente` (todos, em ordem alfabética), `Profissional` (só os ativos, de [[Profissionais]]), `Data`
  (hoje por padrão, pelo dia local) e `Texto do receituário`, vazio, sem exemplo nem modelo, com no máximo 2.000
  caracteres.
- **Imprimir** confere tudo: paciente e profissional escolhidos (o profissional tem de estar ativo), data que existe
  no calendário e texto que não seja só espaço. Faltando algo, o erro aparece no campo e a impressão não abre.
- **A folha** tem: o cabeçalho da clínica (nome, `endereço — cidade/UF`, `Tel.`, do cadastro em [[Clinica]]); o
  título `Receituário`; `Paciente: <nome>` e `Data: DD/MM/AAAA`; o texto, com as quebras de linha e o recuo como
  foram digitados; e, no fim, a linha para assinar à mão com o nome e o CRO do profissional. Do paciente entra só o
  nome: nada de CPF nem de telefone.
- **Só a folha sai no papel**, em preto sobre branco também com o tema escuro. Conferida em A4: uma página para
  um texto de tamanho normal; um texto de 70 linhas passa para a segunda página, e o nome e o CRO vão junto do fim do
  texto, não sozinhos.
- **O formulário segue preenchido depois da impressão**, para imprimir outra via. O texto não é gravado em lugar
  nenhum.

## Movimento e micro-interações

Nenhuma no cartão além das do kit (campos, botão). A folha não tem movimento.

## Limites conhecidos

- **Não guarda nada**: fechar a tela perde o texto. Salvar e reutilizar textos é o item 12.5 do plano.
- **O limite é em caracteres, não em linhas**: um texto com muitas linhas curtas passa de uma página.
- **Paciente é uma lista inteira** (`<select>`), como na marcação de consulta; com centenas de pacientes o próximo
  degrau é uma busca.
- **Sem validade jurídica** e sem assinatura digital: é a linha para assinar à mão.

## Histórico de mudanças

- [[2026-09-30-pr-154-documentos-receituario]] — a tela Documentos e o receituário em texto livre, impresso na folha com linha de assinatura.
