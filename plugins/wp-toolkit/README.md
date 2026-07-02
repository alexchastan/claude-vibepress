# wp-toolkit

Global, install-agnostic commands and skills for working with any WordPress site. Part of the
[VibePress](../../README.md) marketplace.

## Commands

### `/wp-analyze [path-to-wp-root]`

Read-only analysis of a WordPress install. Reports core version, PHP, environment, active theme,
plugins, themes, and notable findings, and saves a copy to `wp-analysis.md` at the WP root.

Uses **WP-CLI** when available (richest data: active status, available updates, multisite, env
type) and falls back to **filesystem parsing** otherwise (works without WP-CLI or a database).
It never changes the site and never prints credentials.

## Install

```
/plugin marketplace add alexchastan/claude-vibepress
/plugin install wp-toolkit@vibepress
```
