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

- **[`vibepress`](./plugins/vibepress)** — the flagship plugin. Provides `/vibepress:scan`, a read-only WordPress scan that writes a concise `CLAUDE.md` (core version, PHP, environment, active theme & plugins) and suggests a theme on fresh installs.

See [`CLAUDE.md`](./CLAUDE.md) for the structure and how to add a plugin.

## Author

Alexandre Chastan
