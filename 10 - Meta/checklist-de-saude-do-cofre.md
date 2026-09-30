---
tipo: meta
ultima_atualizacao: 2026-09-30
tags: [meta]
---

# Checklist de saúde do cofre

Todas as verificações devem imprimir **nada**.

```bash
cd "$ARCADA_VAULT"
# (a) raiz limpa
find . -maxdepth 1 -name '*.md' -not -name 'README.md' -not -name 'CLAUDE.md'
# (b) espaço em nome fora do espinhaço
find . -path ./.git -prune -o -name '* *' -not -name '?? - *' -print
# (c) nota sem tipo:
grep -rL --include='*.md' '^tipo:' . | command grep -vE '^(\./)?(README|CLAUDE)\.md$'
# (d) ADR duplicada
ls "02 - ADRs" | grep -oE '^ADR-[0-9]{3}' | sort | uniq -d
```

Se (c) apontar uma nota, acrescente o frontmatter do tipo dela (tabela no `CLAUDE.md`).
