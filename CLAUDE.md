# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Ce qu'est ce repo

VibePress est une **marketplace de plugins Claude Code** (pas une application). Son but : outiller le « vibecoding » de sites WordPress — scaffolding d'environnement local, thèmes par blocs, blocs Gutenberg, contenu/SEO. Le repo ne se build pas et n'a pas de tests : sa « source » est un ensemble de manifests JSON et de composants de plugins (commandes, agents, skills, hooks, serveurs MCP) que Claude Code découvre et charge.

Auteur/owner : Alexandre Chastan. La marketplace s'appelle `vibepress` (le nom utilisé lors de l'installation).

## Architecture

Deux niveaux de manifest, un seul point d'entrée :

- `.claude-plugin/marketplace.json` — **le manifest de la marketplace**, à la racine du repo. Il déclare `name`, `owner`, `metadata.pluginRoot` (`./plugins`) et le tableau `plugins`. Chaque entrée de `plugins` référence un plugin par un `source` relatif (`"./plugins/<nom>"`) plus des métadonnées.
- `plugins/<nom>/.claude-plugin/plugin.json` — **le manifest de chaque plugin**. `name` est le seul champ requis. En mode strict (défaut), ce fichier fait autorité sur ce que déclare l'entrée marketplace.

État actuel : `plugins: []`. La structure est prête mais aucun plugin n'est encore défini — c'est intentionnel.

### Règle de layout à ne pas confondre

Dans un plugin, **seul `plugin.json` vit dans `.claude-plugin/`**. Tous les autres composants sont à la racine du plugin, pas dans `.claude-plugin/` :

```
plugins/<nom>/
├── .claude-plugin/
│   └── plugin.json          # manifest (seul fichier dans ce dossier)
├── commands/                # commandes slash (*.md)
├── agents/                  # subagents (*.md)
├── skills/<skill>/SKILL.md  # skills (auto-découverts ; s'AJOUTENT au scan par défaut)
├── hooks/hooks.json         # hooks
└── .mcp.json                # serveurs MCP
```

Précédence des chemins déclarés dans `plugin.json` : `commands`/`agents`/`outputStyles` **remplacent** le dossier par défaut ; `skills` **s'ajoute** au scan `skills/`. Dans toute config de composant, utiliser les variables `${CLAUDE_PLUGIN_ROOT}`, `${CLAUDE_PROJECT_DIR}` plutôt que des chemins en dur.

## Ajouter un plugin

1. Créer `plugins/<nom>/.claude-plugin/plugin.json` (`name` en kebab-case, au minimum).
2. Ajouter ses composants à la racine du plugin (`commands/`, `agents/`, `skills/`…).
3. Ajouter une entrée dans `plugins` de `marketplace.json` :
   ```json
   { "name": "<nom>", "source": "./plugins/<nom>", "description": "…", "version": "0.1.0" }
   ```
4. `name` doit être identique dans les deux manifests et en **kebab-case** (minuscules, tirets, pas d'espaces ni underscores).

## Tester localement

La marketplace s'installe depuis un chemin local, sans passer par GitHub :

```
/plugin marketplace add ./            # enregistre cette marketplace depuis le repo local
/plugin install <nom>@vibepress       # installe un plugin de la marketplace
/plugin marketplace update vibepress  # recharge après modification d'un manifest
```

Depuis GitHub, les utilisateurs feront : `/plugin marketplace add alexchastan/claude-vibepress`.

## Conventions

- Tous les `name` (marketplace et plugins) : **kebab-case**.
- Versions en **semver** ; on peut omettre `version` pour un versioning par SHA de commit.
- Le manifest doit rester du JSON valide — c'est le seul artefact qui casse tout s'il est mal formé.
