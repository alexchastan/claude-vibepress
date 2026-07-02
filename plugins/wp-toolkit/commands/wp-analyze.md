---
description: Analyze a WordPress install and produce a site report.
argument-hint: [path-to-wp-root]
allowed-tools: Bash, Read, Glob, Grep, Write
---

# WordPress site analysis

Inspect a WordPress installation and produce a **read-only** report: core version, PHP,
environment, active theme, plugins, themes, and notable findings. Do not change the site.

`$ARGUMENTS` — optional path to the WordPress root. If empty, use the current directory.

## Rules (read first)

- **Read-only.** Never run `wp` write subcommands (no `update`, `install`, `activate`,
  `delete`, `db query` with writes, `eval` with side effects, etc.). Only read/list/get.
- **No secrets.** Never print database credentials, salts, or auth keys from `wp-config.php`.
  Report *presence* of config, never values.
- **Don't assume WordPress.** If you cannot locate a WP install, stop and say so — do not
  invent data.

## Step 1 — Locate the WordPress root

Starting from `$ARGUMENTS` (or the current directory), find the WP root by locating any of:
`wp-config.php`, `wp-load.php`, or `wp-includes/version.php`. Search the given directory and
up to ~3 parent levels. Set `WP_ROOT` to the directory that contains `wp-includes/`.

If none is found, report: "No WordPress installation found at `<path>`." and stop.

## Step 2 — Detect WP-CLI

Check `command -v wp`. If present, probe it against the site:
`wp core version --path="$WP_ROOT"`. If that succeeds, use the **WP-CLI path** (Step 3).
Otherwise (no `wp`, or it errors — e.g. no DB access), use the **filesystem path** (Step 4).
Record which path you used; the report must state it.

## Step 3 — WP-CLI path (preferred)

Run these (all read-only), each with `--path="$WP_ROOT"`:

- `wp core version` — WordPress version
- `wp core check-update --format=json` — is a core update available?
- `wp core is-installed --network` (exit code) — multisite?
- `wp plugin list --format=json --fields=name,status,version,update,update_version`
- `wp theme list --format=json --fields=name,status,version,update`
- `wp eval 'echo PHP_VERSION;'` — server PHP version
- `wp option get blog_public` — 1 = indexable, 0 = discouraged from search engines
- `wp option get siteurl` and `wp option get home`
- `wp config get table_prefix` and, if present, `WP_DEBUG` / `WP_ENVIRONMENT_TYPE`

If a single command fails, note the gap and continue; do not abort the whole run.

## Step 4 — Filesystem path (fallback)

When WP-CLI is unavailable or non-functional:

- **Core version:** read `$WP_ROOT/wp-includes/version.php`, extract `$wp_version`.
- **Plugins:** for each directory in `wp-content/plugins/`, read the main plugin file's header
  block (`Plugin Name:`, `Version:`); also check single-file plugins directly in
  `wp-content/plugins/*.php`. Active/inactive status is **unknown** without the DB — mark it as
  "unknown (no WP-CLI/DB)".
- **Themes:** for each `wp-content/themes/*/style.css`, read the header (`Theme Name:`,
  `Version:`). If a `theme.json` exists, note it's a block theme. Active theme is **unknown**.
- **PHP:** run `php -v` (this is the CLI PHP and may differ from the web server's PHP — flag that).
- **wp-config.php:** report table prefix, whether `WP_DEBUG` / `WP_ENVIRONMENT_TYPE` are set and
  their values, and that DB config is present — **without** printing any credential values.

## Step 5 — Build the report

Produce Markdown with these sections:

1. **Summary** — one-line health snapshot + which collection path was used (WP-CLI / filesystem).
2. **WordPress core** — version, update available?
3. **PHP** — version (note CLI vs server if from filesystem).
4. **Environment** — `WP_ENVIRONMENT_TYPE`, multisite yes/no, `WP_DEBUG` on/off, site/home URL.
5. **Active theme** — name + version (or "unknown" on the filesystem path).
6. **Plugins** — table: Name | Status | Version | Update available.
7. **Themes** — table: Name | Version | Block theme?.
8. **Findings** — flag: outdated core, plugins/themes with updates, `WP_DEBUG` enabled,
   `blog_public = 0` (site hidden from search engines), unusually many inactive plugins, etc.
   If nothing notable, say so.

## Step 6 — Output

Print the full report in the chat, then save a copy to `$WP_ROOT/wp-analysis.md`.
End with a short note listing any data gaps (e.g. "active status unknown — WP-CLI not available").
