# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

- `npm run dev` — Astro dev server.
- `npm run build` — Builds the static site plus the Cloudflare Worker bundle into `dist/` (entry `dist/_worker.js/index.js`, configured in `wrangler.jsonc`).
- `npm run preview` — Preview the production build locally.
- `npm run astro -- <cmd>` — Pass-through to the Astro CLI (e.g. `npm run astro -- check` for type/diagnostic checks; no test runner is configured).

There is no lint or test script. TypeScript uses `astro/tsconfigs/strict`.

## Architecture

This is an Astro v5 site deployed to Cloudflare (via `@astrojs/cloudflare`) at `https://parul.ski`. There is no client-side framework — pages are `.astro` (+ MDX content). Content is authored via Decap CMS, which commits Markdown into the repo.

### Bilingual content model (EN / PL)

The content collection `blog` (`src/content.config.ts`) is split by language using folders:

```
src/content/blog/en/<slug>.md
src/content/blog/pl/<slug>.md
```

Astro derives `post.slug` as `"en/<file>"` / `"pl/<file>"`, but URLs must be `/{lang}/blog/{slug}/` without the `lang/` prefix. Pages strip it via `slug.replace(\`${lang}/\`, '')`. When adding new index/listing code, replicate this normalization rather than using `post.slug` directly.

Each post's frontmatter must include `lang: 'en' | 'pl'`. Use `translationKey` (shared across language variants) to link a post to its translation. The Zod schema in `src/content.config.ts` is the source of truth — Decap's `public/admin/config.yml` must stay in sync with it (currently: `lang` hidden+defaulted, `translationKey` optional, `title`, `description`, `pubDate`, `updatedDate?`, `heroImage?`, `body`).

`heroImage` is a public path like `/uploads/foo.jpg` (not an imported asset), because Decap uploads images into `public/uploads/` and references them by URL.

### Routing

- `src/pages/index.astro` — language picker / latest posts in both languages.
- `src/pages/[lang]/index.astro` — per-language listing; `getStaticPaths` hard-codes `['en','pl']`. Add a new language here AND in the schema enum.
- `src/pages/[lang]/blog/[...slug].astro` — post page. If the `lang` in the URL doesn't match `post.data.lang`, or the slug still carries the `lang/` prefix, it 302-redirects to the canonical URL. Preserve this behavior when refactoring.
- `src/pages/rss.xml.js` — single combined RSS feed for all languages.

### CMS integration

Decap CMS is served as static files from `public/admin/` (`config.yml` + `index.html`) and authenticates through `https://cms.parul.ski` (an OAuth proxy), committing to the `pparulski/blog` repo's `main` branch. Editing the schema in `content.config.ts` without updating `public/admin/config.yml` (or vice versa) will desync the editor from what Astro validates at build time.

### Cloudflare adapter notes

`wrangler.jsonc` uses `nodejs_compat` and `global_fetch_strictly_public`. `public/.assetsignore` controls which files in `public/` are excluded from the Workers static asset upload — check it before adding directories under `public/` you don't want shipped as assets.
