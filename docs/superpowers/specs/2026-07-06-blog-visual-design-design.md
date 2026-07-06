# Blog visual design

## Goal

The blog (Jekyll + GitHub Pages, `minima` theme) currently looks like the stock tutorial theme. This redesigns it to feel elegant with a data/code undertone: readable prose first, with monospace and color reserved for actual code and small technical details rather than decoration.

Direction was shaped by reviewing [stephango.com](https://stephango.com) for its restraint and density, then deliberately diverging from it on typography and per-post metadata (see Decisions below).

## Typography

- **Body, nav, dates, headings**: Inter, everywhere. No serif. Headings are differentiated from body text by weight (600) and size only, not by switching font family.
- **Code**: IBM Plex Mono, for inline `code` and fenced code blocks only. No other element uses a monospace font.
- Fonts are loaded via Google Fonts `<link>` tags (network request at page load; no local font files to maintain).

## Color

- Accent: cool slate-blue — `#4a5a7a` on light backgrounds, `#8fa3c9` on dark.
- Light mode: off-white background (`#fbfbfa`), near-black text (`#23262b`).
- Dark mode: near-black background (`#191b1f`), off-white text (`#d9dbe0`).
- Follows the visitor's OS/browser `prefers-color-scheme`. No manual light/dark toggle.

## Layout

- **Header**: site name (links home) + nav (Home / About), always visible. Unchanged from the current site.
- **Homepage**: reverse-chronological list. Every entry shows date, title, and excerpt (`show_excerpts: true` in `_config.yml`). No pagination changes.
- **Post page**: title, a meta line (date · estimated read time), body copy, code blocks where relevant. No kicker/series label and no tag pill — series context (e.g. "part 1 of 4") lives in the post's own opening prose, not as a UI element.
- **About page**: unchanged, restyled by the same type/color system.

## Decisions made against the stephango.com reference

The reference was useful for layout density and restraint, but three of its choices were deliberately not adopted:

- **No serif headings** — the reference uses a single sans typeface throughout; we're adopting that (dropping an earlier plan to use Source Serif 4 for headings).
- **No "Now" page** — the reference has both About and Now; this site keeps About only.
- **No warm "paper" palette** — the reference uses a warm cream/black palette; this site keeps the cooler neutral + slate-blue palette already validated via mockups.
- **Post list stays dense with excerpts for every entry** — the reference only excerpts its single latest post and reduces the rest of the archive to bare date+title rows. This site keeps title+date+excerpt for every entry regardless of age.

## Implementation approach

Targeted overrides on top of the `minima` gem theme, not a full theme eject and not a hand-rolled theme. Minima already provides pagination, the Atom feed, SEO meta tags, and page-based nav (via `about.md`) — none of that needs to change, so only the following files are added or overridden:

- `assets/main.scss` — overrides minima's stub of the same path. Sets `$base-font-family`, `$text-color`, `$background-color`, and `$brand-color` before `@import "minima";` (these are declared with `!default` upstream, so setting them earlier in the cascade wins), then appends custom rules after the import for heading weight, code-block font/background, and the dark-mode media query.
- `_includes/head.html` — overrides minima's version of the same path, adding the Google Fonts `<link>` tags for Inter and IBM Plex Mono.
- `_layouts/post.html` — overrides minima's version of the same path, adding the estimated read-time calculation next to the date. Computed as `content word count / 200`, rounded up to the nearest minute (Liquid's `ceil` filter) — the standard convention used by most Jekyll read-time snippets.
- `_config.yml` — add `show_excerpts: true`.

**Resolved risk**: confirmed via `https://pages.github.com/versions.json` that GitHub Pages currently builds this site with **Jekyll 3.10.0 / minima 2.5.1**. That version predates minima's native dark-mode support (added in 3.x) and has none of the `custom-variables.scss` / `custom-styles.scss` / `custom-head.html` hook files that exist in later minima versions — those files don't exist in 2.5.1, which is why the approach above overrides `assets/main.scss`, `_includes/head.html`, and `_layouts/post.html` directly instead. Dark mode is implemented as a fully hand-written `prefers-color-scheme` media query rather than relying on any theme-provided support.

## Out of scope

- Manual light/dark toggle (system-preference-only, per Color above)
- Tag/category archive pages
- Syntax-highlighting color theme beyond basic code-block container styling (Rouge's default highlighting classes are left as-is)
- Any change to pagination, feed, or SEO behavior
