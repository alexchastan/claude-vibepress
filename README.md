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

- **[`wp-toolkit`](./plugins/wp-toolkit)** — global, install-agnostic WordPress tools. Provides `/wp-analyze`, a read-only site analysis (core version, PHP, plugins, themes, environment).

See [`CLAUDE.md`](./CLAUDE.md) for the structure and how to add a plugin.

## Author

Alexandre Chastan
