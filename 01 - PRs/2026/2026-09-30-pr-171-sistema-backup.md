---
tipo: pr
data: 2026-09-30
autor: VictorNascimento14
projeto: Arcada
pr: 171
url: https://github.com/VictorNascimento14/Arcada/pull/171
branch: feat/sistema-backup
tags: [pr, sistema, backup, dados, lgpd]
status: merged
---

# PR #171 — feat(sistema): exportar e importar o backup dos dados do navegador

## 🎯 Contexto

Item 14.1 (Backup: exportar e importar) do [[2026-09-30-plano-da-v1]] e primeiro PR do módulo **Sistema**: cria `src/modulos/sistema/` e a rota `/sistema`, que a restauração da demonstração (14.5) e a busca global (14.2) ampliam. Sobre a camada de dados da [[ADR-001-frontend-primeiro-com-dados-locais]] (`localStorage` só em `src/dados/`) e o registro de módulos da [[ADR-003-modulos-por-pasta-com-registro-automatico]]. Fecha a issue #167.

## 🔧 Mudanças

- `src/dados/backup.ts` (+ teste): `exportarBackup`, `lerBackup` (valida sem tocar no armazenamento) e `substituirPor` (troca as chaves e desfaz se a gravação falhar).
- `src/dados/colecao.ts`: `PREFIXO` passa a ser exportado, para o backup não repetir o literal `arcada:`.
- `src/modulos/sistema/`: `modulo.ts` (rota `/sistema`, coluna `cadastros`, ordem 90, ícone `shield-check`), `PaginaSistema.tsx` e `CartaoDeBackup.tsx`, com testes de tela e de rota.

## 🕵️ Dado pessoal (LGPD)

O arquivo de backup é uma cópia de tudo o que o app guarda (pacientes, anamnese, odontograma, evolução clínica, financeiro): dado pessoal sensível, agora fora do navegador e do controle do app. A tela avisa, num `role="note"`, para guardar o arquivo em lugar seguro e não enviá-lo por canal aberto. O arquivo não é cifrado (a v1 é demonstração, sem dado real). Nada sai do aparelho: o download é gerado no navegador (`Blob`) e a importação lê o arquivo localmente. Testes e cofre só usam dado fictício.

## 🧠 Decisões técnicas

- **O arquivo guarda o texto bruto de cada chave** (`dados: { chave: valor }`, exatamente o que estava no `localStorage`), não o JSON já interpretado: o backup não depende de o código entender cada coleção, e a coleção de outro módulo entra sem mudar nada aqui. Em troca, há JSON dentro de texto: o arquivo é para máquina, não para edição à mão.
- **Só a chave `arcada:*` entra e só ela é apagada.** `arcada-tema` e `arcada-sidebar-collapsed` (kit, com traço) ficam fora do prefixo: a aparência não viaja no backup e importar não muda o tema. Na demo publicada a origem é a mesma de todos os projetos do dono, então o prefixo também é o que impede importar ou limpar chave de outro app.
- **A validação vem antes de tudo e não toca no armazenamento** (`lerBackup`): JSON, `app`, versão (inteira, ≥ 1; maior que a do app vira «versão mais nova», peça para atualizar), data da exportação e, em cada chave sob `arcada:`, o formato de coleção `{ versao, itens[].id }` — o mesmo que `colecao.ts` exige na escrita. Só então a tela pede confirmação.
- **Gravação com desfazer.** `substituirPor` tira uma cópia do que há, apaga as chaves do Arcada e grava as do backup; se `setItem` lançar (cota cheia), devolve a cópia e retorna o erro. Teto conhecido (`ponytail:` no código): se até devolver a cópia falhar, o disco pode ficar incompleto.
- **Recarregar depois de importar.** As coleções guardam o estado em memória e o próximo `salvar` gravaria o estado antigo por cima do importado; por isso a tela chama `location.reload()`. A regra de dados não recarrega: quem chama decide.
- **Erro como frase, não exceção**: `lerBackup` devolve `{ backup } | { erro }` e `substituirPor` devolve a frase de erro ou `undefined`, no estilo de `adicionarConvenio`.
- **Sem nada salvo, não baixa arquivo vazio**: um backup sem dados daria falsa sensação de segurança (acontece com o armazenamento bloqueado).

## ⚠️ Armadilhas e aprendizados

- `location.reload()` não roda no jsdom: o teste de tela troca `location` por um objeto com `reload` dublê (`vi.stubGlobal`), põe um dublê em `URL.createObjectURL` e captura o clique do `<a download>` em `HTMLAnchorElement.prototype.click`.
- O `input type=file` tem o `value` zerado depois de ler o arquivo: sem isso, escolher o mesmo arquivo duas vezes seguidas (por exemplo, depois de corrigi-lo) não dispara `change`.
- `exportadoEm` é um instante em UTC (`toISOString`), não um dia: o nome do arquivo usa `diaISO(new Date(exportadoEm))` para o dia local e o modal mostra data e hora no fuso do navegador.
- A marca de semente (`arcada:sementes:*`) guarda só a versão, sem o envelope de coleção: a validação a trata à parte.

## 🧪 Como testar

1. Abra `/sistema` (item **Sistema**, em Cadastros) e clique em **Exportar backup**: baixa `arcada-backup-<dia>.json`. Abra o arquivo e confira `app: "arcada"`, `versao: 1` e só chaves `arcada:*` em `dados` (nenhuma `arcada-tema`).
2. Mude um dado (por exemplo, cadastre um paciente), clique em **Importar backup** e escolha o arquivo exportado antes: abre «Substituir os dados atuais?» com a data do backup. **Cancelar** não muda nada.
3. Repita e confirme em **Substituir os dados**: a página recarrega e o paciente novo some (voltam os dados do arquivo).
4. Importe um JSON qualquer: aparece o motivo em vermelho («Este arquivo não é um backup do Arcada.»), sem modal e sem alterar nada.

## 📎 Documentação afetada

- [[Sistema]]
- [[ADR-001-frontend-primeiro-com-dados-locais]]
- [[ADR-003-modulos-por-pasta-com-registro-automatico]]
- [[2026]] (changelog)
