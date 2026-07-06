# Blog Visual Design Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restyle the Jekyll blog (currently stock `minima`) to match the validated design: Inter typography throughout (headings differentiated by weight, not font family), IBM Plex Mono reserved for code, a slate-blue accent with automatic light/dark mode, and a read-time indicator on posts.

**Architecture:** Targeted overrides on top of the `minima` gem theme — no theme ejection, no hand-rolled theme. Four files are added/overridden (`_config.yml`, `_includes/head.html`, `assets/main.scss`, `_layouts/post.html`); everything else (pagination, feed, SEO tags, page-based nav) is left to minima untouched.

**Tech Stack:** Jekyll (GitHub Pages legacy build), Ruby Sass via the `minima` 2.5.1 theme, Liquid templating, Google Fonts (Inter, IBM Plex Mono).

## Global Constraints

- GitHub Pages builds this site with its legacy pinned toolchain: **Jekyll 3.10.0, minima 2.5.1** (confirmed via `https://pages.github.com/versions.json`). This version predates minima's native dark-mode support (added in minima 3.x) and has none of the `custom-variables.scss` / `custom-styles.scss` / `custom-head.html` hook files that exist in later minima versions — those do **not** exist in 2.5.1, so overrides target `assets/main.scss`, `_includes/head.html`, and `_layouts/post.html` directly.
- **No local Jekyll build is available.** This machine's system Ruby is 2.6.10 and several of Jekyll's runtime dependencies (`em-websocket` → `eventmachine`, modern `ffi`) fail to compile natively here (missing `pkg-config`/OpenSSL linkage for native extensions, and newer `ffi` requires Ruby ≥ 3.0). Per explicit user direction, **do not** try to fix the local Ruby toolchain or add a Gemfile for this — GitHub Pages' legacy builder ignores any repo Gemfile anyway. **Verification is "test in prod"**: push to `main`, let GitHub's Pages builder rebuild, then `curl` the live site and check the actual output.
- For Sass-only tasks, a lightweight **local sanity compile** is available using Dart Sass via `npx` (no native extensions, already confirmed to work) against vendored copies of minima 2.5.1's real Sass partials. This is not the same engine GitHub Pages uses (libsass, via `jekyll-sass-converter` 1.5.2) but is close enough to catch syntax errors and confirm compiled values before pushing. Steps below show the exact vendoring commands.
- Colors: accent `#4a5a7a` (light) / `#8fa3c9` (dark); background `#fbfbfa` (light) / `#191b1f` (dark); text `#23262b` (light) / `#d9dbe0` (dark).
- Fonts: `Inter` (400/500/600) for everything except code; `IBM Plex Mono` (400/500) for inline `code` and `pre` blocks only.
- Read time: `word count / 200`, rounded up (`ceil`), per the approved spec.
- Spec reference: `docs/superpowers/specs/2026-07-06-blog-visual-design-design.md`.

---

### Task 1: Config — excerpts on, keep internal docs out of the build

**Files:**
- Modify: `_config.yml`

**Interfaces:**
- Produces: `site.show_excerpts` (consumed by minima's stock `_layouts/home.html`, already present in the gem — no override needed for the homepage excerpt display).

- [ ] **Step 1: Sync with main first**

```bash
git fetch origin
git rebase origin/main
```

`starting-point.md` may already be excluded via a separate PR (#3) by the time this runs. The target content below is idempotent either way — if `exclude:` already has `starting-point.md`, just add `docs` to the same list instead of duplicating the block.

- [ ] **Step 2: Update `_config.yml`**

Write the full file as:

```yaml
theme: minima
title: steffen novak mollestad
description: a place for where thoughts turns into writing
author: steffenmoll

show_excerpts: true

exclude:
  - starting-point.md
  - docs
```

(`docs` keeps this plan and the design spec out of the published site — the same class of bug fixed for `starting-point.md` in PR #3: any file without Jekyll front matter under a non-underscore, non-dot directory gets copied verbatim into the built site unless excluded.)

- [ ] **Step 3: Validate YAML syntax**

```bash
ruby -ryaml -e "YAML.load_file('_config.yml'); puts 'OK'"
```

Expected output: `OK`

- [ ] **Step 4: Commit**

```bash
git add _config.yml
git commit -m "Enable post excerpts and exclude internal docs from the build"
```

---

### Task 2: Fonts — load Inter and IBM Plex Mono

**Files:**
- Create: `_includes/head.html` (overrides the gem's version of the same path)

**Interfaces:**
- Produces: Inter and IBM Plex Mono become available site-wide once `assets/main.scss` (Task 3) references them by name.

- [ ] **Step 1: Write the overridden file**

minima 2.5.1's own `_includes/head.html` (verified from `https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_includes/head.html`) is:

```html
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {%- seo -%}
  <link rel="stylesheet" href="{{ "/assets/main.css" | relative_url }}">
  {%- feed_meta -%}
  {%- if jekyll.environment == 'production' and site.google_analytics -%}
    {%- include google-analytics.html -%}
  {%- endif -%}
</head>
```

Create `_includes/head.html` with the font `<link>` tags added before the stylesheet link:

```html
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {%- seo -%}
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="{{ "/assets/main.css" | relative_url }}">
  {%- feed_meta -%}
  {%- if jekyll.environment == 'production' and site.google_analytics -%}
    {%- include google-analytics.html -%}
  {%- endif -%}
</head>
```

- [ ] **Step 2: Confirm the diff only adds the three font lines**

```bash
git diff --stat _includes/head.html
```

Expected: 1 file changed (new file), and a manual read confirms the only additions relative to minima's base are the two `preconnect` lines and the Google Fonts stylesheet `link`.

- [ ] **Step 3: Commit**

```bash
git add _includes/head.html
git commit -m "Load Inter and IBM Plex Mono via Google Fonts"
```

---

### Task 3: Base typography and color palette

**Files:**
- Create: `assets/main.scss` (overrides the gem's stub of the same path)

**Interfaces:**
- Consumes: minima's `$base-font-family`, `$text-color`, `$background-color`, `$brand-color` Sass variables (all declared with `!default` in the gem's `minima.scss`, so setting them before `@import "minima";` wins).
- Produces: the compiled `assets/main.css` that `_includes/head.html` (Task 2) already links to.

- [ ] **Step 1: Write the file**

minima 2.5.1's stub (verified from `https://raw.githubusercontent.com/jekyll/minima/v2.5.1/assets/main.scss`) is just:

```scss
---
# Only the main Sass file needs front matter (the dashes are enough)
---

@import "minima";
```

Replace it with:

```scss
---
# Only the main Sass file needs front matter (the dashes are enough)
---

$base-font-family: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
$text-color: #23262b;
$background-color: #fbfbfa;
$brand-color: #4a5a7a;

@import "minima";
```

- [ ] **Step 2: Vendor minima 2.5.1's real Sass partials for a local compile check**

Local Jekyll isn't available (see Global Constraints), so validate with Dart Sass against the actual upstream partials instead of guessing:

```bash
SCRATCH="$(mktemp -d)"
mkdir -p "$SCRATCH/_sass/minima"
curl -s "https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_sass/minima.scss" -o "$SCRATCH/_sass/minima.scss"
curl -s "https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_sass/minima/_base.scss" -o "$SCRATCH/_sass/minima/_base.scss"
curl -s "https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_sass/minima/_layout.scss" -o "$SCRATCH/_sass/minima/_layout.scss"
curl -s "https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_sass/minima/_syntax-highlighting.scss" -o "$SCRATCH/_sass/minima/_syntax-highlighting.scss"
echo "$SCRATCH" > /tmp/blog-sass-scratch-path
```

- [ ] **Step 3: Compile `assets/main.scss` (front matter stripped) and check the output**

```bash
SCRATCH="$(cat /tmp/blog-sass-scratch-path)"
tail -n +4 assets/main.scss > "$SCRATCH/main.scss"
cd "$SCRATCH" && npx --yes sass --load-path=_sass main.scss out.css
grep -E "^\s*(font|color|background-color):" out.css | head -10
cd - > /dev/null
```

Expected: exit code 0 (deprecation warnings from old Sass syntax are expected and harmless — libsass, what GitHub Pages actually uses, won't emit them). The grep output should include `color: #23262b;`, `background-color: #fbfbfa;`, and `color: #4a5a7a;` among the results, confirming the overrides took effect.

- [ ] **Step 4: Commit**

```bash
git add assets/main.scss
git commit -m "Set Inter as the base font and apply the slate-blue palette"
```

---

### Task 4: Heading weight and code styling

**Files:**
- Modify: `assets/main.scss`

**Interfaces:**
- Consumes: same file as Task 3 — appends rules after `@import "minima";`.

- [ ] **Step 1: Append heading and code rules**

Add to the end of `assets/main.scss` (after the `@import "minima";` line from Task 3):

```scss

h1, h2, h3, h4, h5, h6,
.site-title,
.post-title,
.post-link {
  font-weight: 600;
}

.site-title,
.site-title:visited {
  color: $text-color;
}

pre,
code {
  font-family: "IBM Plex Mono", ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
  background-color: #eef0f4;
}
```

(minima's own `pre, code` rule earlier in the cascade sets `background-color: #eef;` and no font-family override — ours appears later in the same compiled stylesheet with equal selector specificity, so it wins per normal CSS cascade rules. `.site-title` isn't tied to `$text-color` in minima's source — it hardcodes `$grey-color-dark` — so it needs its own override to match the rest of the heading treatment.)

- [ ] **Step 2: Recompile and check**

```bash
SCRATCH="$(cat /tmp/blog-sass-scratch-path)"
tail -n +4 assets/main.scss > "$SCRATCH/main.scss"
cd "$SCRATCH" && npx --yes sass --load-path=_sass main.scss out.css
grep -A2 "^pre," out.css | tail -5
grep -B1 "font-weight: 600" out.css | head -20
cd - > /dev/null
```

Expected: the last `pre { ... }` block in `out.css` shows `font-family: "IBM Plex Mono", ...` and `background-color: #eef0f4;`, and several selectors (`h1`, `.post-title`, `.site-title`, etc.) show `font-weight: 600;`.

- [ ] **Step 3: Commit**

```bash
git add assets/main.scss
git commit -m "Differentiate headings by weight and set code to IBM Plex Mono"
```

---

### Task 5: Dark mode

**Files:**
- Modify: `assets/main.scss`

**Interfaces:**
- Consumes: same file as Tasks 3–4.

- [ ] **Step 1: Append the dark-mode media query**

minima 2.5.1 has no dark-mode support at all, so every relevant selector needs an explicit override rather than swapping a small set of CSS custom properties. Add to the end of `assets/main.scss`:

```scss

@media (prefers-color-scheme: dark) {
  body {
    background-color: #191b1f;
    color: #d9dbe0;
  }

  a,
  a:visited {
    color: #8fa3c9;
  }

  a:hover {
    color: #d9dbe0;
  }

  .site-title,
  .site-title:visited {
    color: #d9dbe0;
  }

  .post-meta {
    color: #8b8e94;
  }

  pre,
  code {
    background-color: #232a35;
    border-color: #2b323d;
  }

  .site-header {
    border-top-color: #2b323d;
    border-bottom-color: #2b323d;
  }

  .site-footer {
    border-top-color: #2b323d;
  }

  blockquote {
    color: #b7bac0;
    border-left-color: #2b323d;
  }

  table {
    color: #d9dbe0;

    tr:nth-child(even) {
      background-color: #1f2126;
    }

    th,
    td {
      border-color: #2b323d;
    }
  }
}
```

- [ ] **Step 2: Recompile and check**

```bash
SCRATCH="$(cat /tmp/blog-sass-scratch-path)"
tail -n +4 assets/main.scss > "$SCRATCH/main.scss"
cd "$SCRATCH" && npx --yes sass --load-path=_sass main.scss out.css
grep -c "prefers-color-scheme: dark" out.css
grep -A3 "prefers-color-scheme" out.css | head -6
cd - > /dev/null
```

Expected: `grep -c` returns `1`, and the media query block appears in the compiled output with `background-color: #191b1f;` as its first declaration.

- [ ] **Step 3: Commit**

```bash
git add assets/main.scss
git commit -m "Add dark mode via prefers-color-scheme"
```

---

### Task 6: Read time on posts

**Files:**
- Create: `_layouts/post.html` (overrides the gem's version of the same path)

**Interfaces:**
- Consumes: Liquid's built-in `strip_html`, `number_of_words`, `divided_by`, and `ceil` filters (all standard Jekyll/Liquid filters, no plugin required).
- Produces: no downstream consumers — this is the last content-facing change in the plan.

- [ ] **Step 1: Write the overridden file**

minima 2.5.1's own `_layouts/post.html` (verified from `https://raw.githubusercontent.com/jekyll/minima/v2.5.1/_layouts/post.html`) is:

```html
---
layout: default
---
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time class="dt-published" datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        {{ page.date | date: date_format }}
      </time>
      {%- if page.author -%}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span class="p-author h-card" itemprop="name">{{ page.author }}</span></span>
      {%- endif -%}</p>
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {{ content }}
  </div>

  {%- if site.disqus.shortname -%}
    {%- include disqus_comments.html -%}
  {%- endif -%}

  <a class="u-url" href="{{ page.url | relative_url }}" hidden></a>
</article>
```

Create `_layouts/post.html` with the read-time calculation added to the meta line:

```html
---
layout: default
---
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time class="dt-published" datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        {{ page.date | date: date_format }}
      </time>
      {%- assign words = content | strip_html | number_of_words -%}
      {%- assign read_time = words | divided_by: 200.0 | ceil -%}
      · {{ read_time }} min read
      {%- if page.author -%}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span class="p-author h-card" itemprop="name">{{ page.author }}</span></span>
      {%- endif -%}</p>
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {{ content }}
  </div>

  {%- if site.disqus.shortname -%}
    {%- include disqus_comments.html -%}
  {%- endif -%}

  <a class="u-url" href="{{ page.url | relative_url }}" hidden></a>
</article>
```

- [ ] **Step 2: Sanity-check the read-time math by hand**

The existing post `_posts/2026-07-06-one-platform-does-not-solve-your-data-problem.md` has roughly 480 words in its body. `480 / 200 = 2.4`, `ceil(2.4) = 3`. Confirm the word count and expected result:

```bash
python3 -c "
import re
with open('_posts/2026-07-06-one-platform-does-not-solve-your-data-problem.md') as f:
    text = f.read().split('---', 2)[2]
words = len(re.findall(r'\S+', text))
print('words:', words, '-> expected min read:', -(-words // 200))
"
```

Expected: prints a word count and an expected read-time of 2 or 3 minutes — this is the value to look for once the post is live (Task 7).

- [ ] **Step 3: Commit**

```bash
git add _layouts/post.html
git commit -m "Show estimated read time on posts"
```

---

### Task 7: Merge and verify in production

**Files:** none (integration checkpoint)

**Interfaces:** none — this task confirms the deployed output of Tasks 1–6 together.

- [ ] **Step 1: Push the branch and open a PR**

```bash
git push -u origin blog-visual-design
gh pr create --title "Restyle the blog: Inter + IBM Plex Mono, slate-blue palette, dark mode, read time" --body "Implements docs/superpowers/specs/2026-07-06-blog-visual-design-design.md. See that spec for the full rationale, including deliberate divergences from the stephango.com reference."
```

- [ ] **Step 2: Wait for user approval and merge**

GitHub Pages' legacy builder only builds from `main`, and merging is a shared-state action — **do not merge this PR without the user's explicit go-ahead.** Pause here and ask.

- [ ] **Step 3: After merge, wait for the Pages build to finish**

```bash
gh run list --workflow=pages-build-deployment --limit 1 --json status,conclusion
```

Poll until `status` is `completed`. Expected `conclusion`: `success`.

- [ ] **Step 4: Verify the live site**

```bash
curl -s https://steffenmoll.github.io/writing/ | grep -o 'fonts.googleapis[^"]*'
curl -s https://steffenmoll.github.io/writing/assets/main.css | grep -o "background-color:#fbfbfa"
curl -s https://steffenmoll.github.io/writing/assets/main.css | grep -c "prefers-color-scheme:dark"
curl -s https://steffenmoll.github.io/writing/2026/07/06/one-platform-does-not-solve-your-data-problem.html | grep -o '[0-9]* min read'
curl -s -o /dev/null -w "%{http_code}\n" https://steffenmoll.github.io/writing/starting-point.md
curl -s -o /dev/null -w "%{http_code}\n" https://steffenmoll.github.io/writing/docs/superpowers/specs/2026-07-06-blog-visual-design-design.md
```

Expected: the Google Fonts URL is present; `background-color:#fbfbfa` is present in the compiled CSS; the dark-mode media query count is `1` or more; the post page shows a "`N min read`" string; and both the `starting-point.md` and `docs/...` requests return `404`.

- [ ] **Step 5: Report results to the user**

Summarize what passed/failed from Step 4 and, if anything is wrong, fix forward with a new small commit rather than reverting the merge.
