# claude-skills

Mes skills personnels pour Claude Code, partagés entre mes machines.

## Skills

- **pdf-docs** : produit un PDF (Typst) et le range dans le projet : cours → `course/`, audit de code → `audit/`, roadmap/planning → `planning/`.

## Installation sur une nouvelle machine

```bash
git clone git@github.com:My-environment/claude-skills.git ~/claude-skills
ln -s ~/claude-skills ~/.claude/skills
```

Si `~/.claude/skills` existe déjà en tant que dossier, déplacez son contenu dans `~/claude-skills` puis supprimez-le avant de créer le lien. `pdf-docs` nécessite [Typst](https://typst.app) (`cargo install --locked typst-cli` ou `brew install typst`).

Le dossier `synced/` (skills Anthropic gérés par Claude Code) est ignoré par git.
