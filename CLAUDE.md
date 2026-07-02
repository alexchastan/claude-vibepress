# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> Authoring language: all repository files (manifests, skills, plugins, docs) are written in **English**, regardless of the language used to converse with the user. Skill/plugin instructions are more reliable in English.

## What this repo is

VibePress is a **Claude Code plugin marketplace** (not an application). Its purpose: tooling to "vibecode" WordPress sites — local environment scaffolding, block themes, Gutenberg blocks, content/SEO. There is nothing to build and no test suite: the repo's "source" is a set of JSON manifests and plugin components (commands, agents, skills, hooks, MCP servers) that Claude Code discovers and loads.

Owner: Alexandre Chastan. The marketplace is named `vibepress` (the name used when installing).

## Architecture

Two manifest levels, one entry point:

- `.claude-plugin/marketplace.json` — **the marketplace manifest**, at the repo root. Declares `name`, `owner`, `metadata.pluginRoot` (`./plugins`), and the `plugins` array. Each `plugins` entry references a plugin by a relative `source` (`"./plugins/<name>"`) plus metadata.
- `plugins/<name>/.claude-plugin/plugin.json` — **each plugin's manifest**. `name` is the only required field. In strict mode (default), this file is authoritative over what the marketplace entry declares.

Current state: `plugins: []`. The structure is ready but no plugin is defined yet — this is intentional.

### Layout rule not to confuse

Inside a plugin, **only `plugin.json` lives in `.claude-plugin/`**. Every other component sits at the plugin root, not in `.claude-plugin/`:

```
plugins/<name>/
├── .claude-plugin/
│   └── plugin.json          # manifest (only file in this folder)
├── commands/                # slash commands (*.md)
├── agents/                  # subagents (*.md)
├── skills/<skill>/SKILL.md  # skills (auto-discovered; ADD to the default scan)
├── hooks/hooks.json         # hooks
└── .mcp.json                # MCP servers
```

Path precedence for fields declared in `plugin.json`: `commands`/`agents`/`outputStyles` **replace** the default folder; `skills` **adds** to the `skills/` scan. In any component config, use the `${CLAUDE_PLUGIN_ROOT}`, `${CLAUDE_PROJECT_DIR}` variables rather than hard-coded paths.

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` (kebab-case `name`, at minimum).
2. Add its components at the plugin root (`commands/`, `agents/`, `skills/`…).
3. Add an entry to `plugins` in `marketplace.json`:
   ```json
   { "name": "<name>", "source": "./plugins/<name>", "description": "…", "version": "0.1.0" }
   ```
4. `name` must match in both manifests and be **kebab-case** (lowercase, hyphens, no spaces or underscores).

## Testing locally

The marketplace installs from a local path, without going through GitHub:

```
/plugin marketplace add ./            # register this marketplace from the local repo
/plugin install <name>@vibepress      # install a plugin from the marketplace
/plugin marketplace update vibepress  # reload after editing a manifest
```

From GitHub, users will run: `/plugin marketplace add alexchastan/claude-vibepress`.

## Conventions

- All `name` values (marketplace and plugins): **kebab-case**.
- Versions in **semver**; `version` may be omitted for commit-SHA versioning.
- The manifest must stay valid JSON — it is the one artifact that breaks everything if malformed.
