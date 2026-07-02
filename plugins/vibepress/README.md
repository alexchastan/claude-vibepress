# vibepress

The flagship [VibePress](../../README.md) plugin: install-agnostic commands to scan and vibecode any
WordPress site.

## Commands

### `/vibepress:scan [path-to-wp-root]`

Read-only scan of a WordPress install that writes a **concise `CLAUDE.md`** block at the WP root —
core version, PHP, environment, active theme, and active plugins. The block sits between
`<!-- vibepress:start -->` / `<!-- vibepress:end -->` markers, so re-runs update in place and never
clobber existing `CLAUDE.md` content. It is intentionally minimal: `CLAUDE.md` is auto-loaded into
context every session.

Uses **WP-CLI** when available (active status, updates, multisite, env type) and falls back to
**filesystem parsing** otherwise. Never changes the site, never prints credentials. On a **fresh
install** it chains straight into `/vibepress:theme-create`.

### `/vibepress:theme-create [theme-name]`

Guided creation of a new theme. Walks an interview — theme type (Block/FSE, ACF Pro, child, classic
PHP), name, and design source (Figma, mockups, a Claude/online URL, or a from-scratch design-system
form) — then **scaffolds** the theme (zero-build), seeds **design tokens** into `theme.json` or
CSS variables, writes a theme-level **`CLAUDE.md`** (coding conventions) and **`DESIGN.md`** (design
system), and **activates** it (with confirmation). Fonts are left to WordPress' native Font Library.

## Install

```
/plugin marketplace add alexchastan/claude-vibepress
/plugin install vibepress@vibepress
```
