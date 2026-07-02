---
description: Guided creation of a new WordPress theme (scaffold + design tokens + docs + activate).
argument-hint: [theme-name]
---

# Create a WordPress theme (guided)

Walk the user through a precise interview, then **scaffold** a new theme, seed its **design tokens**,
write a theme-level **`CLAUDE.md`** and **`DESIGN.md`**, and **activate** it. Zero-build (plain
CSS/JS + `theme.json`, no npm).

This command **changes the site** (writes files, activates a theme). Confirm before writing and before
activating. This flow can be started directly (`/vibepress:theme-create`) or continued from
`/vibepress:scan` on a fresh install — in that case the WP root and language are already known, so
skip re-detection (Step 0).

## Rules (read first)

- **Confirm before mutating.** Show a recap before scaffolding; confirm again before `wp theme
  activate`. Never activate silently.
- **No secrets.** Never read out or write credentials/salts from `wp-config.php`.
- **Language.** Run the interview and replies in the user's language (conversation → site locale →
  English). All **files** you write (theme code, `DESIGN.md`, `CLAUDE.md`) are in **English**.
- **Zero-build.** No `package.json`, no `node_modules`, no bundler. Plain CSS/JS + `theme.json`.
- **Fonts.** Do not bake in CDN or self-hosted fonts. Declare font *families* in `theme.json` for
  block themes (managed via the WordPress Font Library, Appearance → Editor → Styles), and use a
  neutral system font stack for classic/ACF themes.

## Step 0 — Preconditions (skip if chained from scan)

Locate the WP root (find `wp-includes/version.php` / `wp-content/themes/`, searching cwd/`$ARGUMENTS`
and up to ~3 parents). If none, say so and stop. Detect WP-CLI (`command -v wp` + `wp core version
--path="$WP_ROOT"`) — used for listing parent themes, ACF detection, and activation. Determine the
reply language.

## Step 1 — Interview (one `AskUserQuestion` decision at a time)

1. **Theme type** — Block (FSE) · ACF Pro · Child theme · Classic PHP · Other.
   - **Child** → ask for the **parent**: list installed themes (`wp theme list --format=csv
     --fields=name,status` or enumerate `wp-content/themes/*/style.css` "Theme Name"); the user picks
     one. Note whether the parent is a block theme (has `theme.json`) or classic.
   - **ACF Pro** → check ACF Pro is active (`wp plugin list --format=csv | grep advanced-custom-fields`,
     or a plugin dir named `advanced-custom-fields-pro`). If absent, warn and ask whether to proceed.
2. **Theme name** — free text (default to `$ARGUMENTS` if provided). Derive the **slug** (kebab-case,
   lowercase). If `wp-content/themes/<slug>` already exists, ask: overwrite, or pick a new name.
3. **Design source** — "provide any you have" (more than one is fine), or start from scratch:
   - **Figma link** → use the Figma MCP: `get_variable_defs` (tokens/variables), `get_design_context`,
     `get_screenshot` for reference. Map variables → color/type/spacing tokens.
   - **Mockup images** (user uploads) → read them and *approximate* palette, type scale, spacing.
     State clearly that these are approximated from images.
   - **Claude design URL / existing site URL** → `WebFetch` the page; optionally use chrome-devtools to
     read computed styles. Extract palette, fonts, spacing.
   - **Start from scratch** → run the mini form below.
4. **Recap & confirm** — summarize type, name/slug, parent (if any), design source, and the resolved
   token set. Let the user adjust before anything is written.

### Mini design-system form (from scratch)

Ask briefly, applying best-practice defaults so the result is sane without deep input:
- **Vibe/personality** (e.g. minimal, editorial, bold, corporate, playful) — steers palette & type.
- **Primary brand color** (hex) → derive an accessible palette: primary + shades, a neutral gray
  scale, and semantic `success`/`warning`/`error`, all meeting **WCAG AA** contrast for text.
- **Type scale** — modular ~1.25; weights for headings/body. Font *families*: system stack by default
  (see Fonts rule).
- **Spacing** — 8-pt scale: `4, 8, 12, 16, 24, 32, 48, 64`.
- **Radius & density** — sensible defaults (e.g. radius `8px`, comfortable density).

These answers become the token set used in Step 2–4.

## Step 2 — Scaffold (zero-build) at `wp-content/themes/<slug>/`

Common to all: valid `style.css` header (Theme Name, Version `0.1.0`, Text Domain `<slug>`,
Description, Author). Seed tokens into code as below.

- **Block (FSE)** — `style.css`, `theme.json` (`"$schema": "https://schemas.wp.org/trunk/theme.json"`,
  `"version": 3`), `templates/index.html`, `parts/header.html`, `parts/footer.html`, one starter
  `patterns/hero.php`, `functions.php` (theme supports, enqueue `style.css`, register a pattern
  category). Tokens → `settings.color.palette`, `settings.typography.fontFamilies` + `fontSizes`,
  `settings.spacing.spacingSizes`; extras → `settings.custom`.
- **Classic PHP** — `style.css`, `functions.php` (load text domain + `wp_enqueue_style` for
  `tokens.css` then `style.css`), `index.php`, `header.php`, `footer.php`, `template-parts/`,
  `assets/css/tokens.css` (`:root { --color-…; --space-…; --font-… }` from tokens), `assets/css/style.css`.
- **ACF Pro** — everything in Classic **plus** `acf-json/` (empty, for field-group sync) with
  save/load points registered in `functions.php` (`acf/settings/save_json`, `acf/settings/load_json`),
  `template-parts/blocks/` for ACF blocks, and one sample flexible-content group + render template.
- **Child theme** — `style.css` with `Template: <parent-slug>`, `functions.php` enqueuing parent then
  child styles. If the parent is a block theme, add a `theme.json` that overrides tokens; otherwise a
  `tokens.css`. Child tokens override the parent's.

## Step 3 — Write `DESIGN.md` (theme dir, English)

The design system, concise and structured: source-of-truth note (Figma/mockups/URL/from-scratch);
**color palette** (token → hex → role); **typography** (families/scale/weights/line-height);
**spacing/radii/shadows/breakpoints**; **component conventions** (button/form/card + states);
**a11y** (AA contrast, visible focus); and a **token → code map** (which `theme.json` preset key or
CSS variable each token corresponds to). Keep hex values consistent with what Step 2 wrote.

## Step 4 — Write theme `CLAUDE.md` (theme dir, English)

Coding conventions, loaded whenever an agent works inside this theme:
- **Identity** — name, slug, type, parent (if child), WP + PHP targets, text domain.
- **Structure map** — where templates/parts/patterns/blocks/ACF groups/assets live.
- **How-to** — add a template/part/pattern; (ACF) add a field group via `acf-json` sync; enqueue an
  asset; register a block.
- **Conventions** — naming, i18n (text domain, translation functions), escaping/sanitization, WPCS,
  **zero-build** (edit CSS/JS directly — no build step).
- **Pointer** — "Design tokens and rules live in `DESIGN.md` — follow it."

## Step 5 — Activate

If WP-CLI is available: **confirm**, then `wp theme activate <slug> --path="$WP_ROOT"`; verify with
`wp theme list --status=active`. If not: skip activation and tell the user how (Appearance → Themes,
or `wp theme activate <slug>`).

## Step 6 — Reply (user's language, terse)

One short recap: theme created (and activated, or how to activate), and the paths to `DESIGN.md` and
`CLAUDE.md`. Suggest next steps: start building templates, and re-run `/vibepress:scan` to refresh the
root `CLAUDE.md`'s active-theme line. Do not paste file contents.
