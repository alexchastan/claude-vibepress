# VibePress

A [Claude Code](https://claude.com/claude-code) plugin marketplace for **vibecoding WordPress sites**: local environment scaffolding, block themes, Gutenberg blocks, content, and SEO.

> Status: bootstrapping. The marketplace is in place; plugins are on the way.

## Install

```
/plugin marketplace add alexchastan/claude-vibepress
/plugin install <plugin-name>@vibepress
```

Locally, from a clone of the repo:

```
/plugin marketplace add ./
```

## Plugins

- **[`vibepress`](./plugins/vibepress)** — the flagship plugin.
  - `/vibepress:scan` — read-only scan that writes a concise `CLAUDE.md` (core version, PHP, environment, active theme & plugins); on a fresh install it chains into theme creation.
  - `/vibepress:theme-create` — guided theme scaffolding (Block/FSE, ACF Pro, child, classic PHP) with design tokens, a theme-level `CLAUDE.md` + `DESIGN.md`, and activation.

See [`CLAUDE.md`](./CLAUDE.md) for the structure and how to add a plugin.

## Author

Alexandre Chastan
