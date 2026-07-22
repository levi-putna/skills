---
name: seo
id: d97a2fdd-7efe-4d99-93d2-ea3cc37dfd30
version: 1.0.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  Review or improve a page's or post's search-engine optimisation: metadata,
  canonical URLs, structured data (JSON-LD), sitemap/robots entries, heading
  structure, and image alt text. Use when asked to "improve SEO", "optimize
  for search", "check/fix meta tags", "add structured data", "audit SEO",
  or "sitemap optimization" for a page, post, or the whole site. Works
  against any Next.js App Router project: reuses whatever metadata/sitemap/
  structured-data helpers it already has, and falls back to the standard
  Next.js APIs directly when it has none.
---

# SEO

Reviews and fixes on-page and technical SEO for Next.js App Router pages
(and any MDX/CMS-driven content on top of them), grounded in a maintained
checklist. This is a review-and-extend skill, not a from-scratch setup: it
works with whatever SEO infrastructure a project already has - discover it
first, extend it, and only fall back to hand-written metadata/JSON-LD when
the project genuinely has no shared helper yet.

The checklist itself lives in a separate, human-maintained reference doc:

```
.skills/seo/references/seo-guidelines.md
```

**Always re-read that file fresh at the start of every review** rather than
relying on memory of a previous run - it's the source of truth and the user
edits it independently of this skill. Don't inline its content into this
file or let it go stale in a summary.

## Workflow

1. **Discover the project's SEO conventions.** Before auditing or fixing
   anything, work out what this specific project already has - file names
   and structure vary project to project, so search rather than assume:

   | What to find | Look for | If it exists | If it doesn't |
   |---|---|---|---|
   | Sitemap | `sitemap.js`/`.ts` under the App Router root - Next.js's built-in sitemap convention | Note whether it's a hardcoded static list, a mapped content source, or both | There's no sitemap yet - flag it as a gap before adding routes to check against it |
   | Robots | `robots.js`/`.ts` under the App Router root - Next.js's built-in convention | Check the target route isn't disallowed | Next.js serves permissive default behaviour with no `robots.js`; note this rather than assuming rules exist |
   | Shared metadata helper | A module (commonly under `lib/`, `utils/`, `helpers/`) with a function that centralises Open Graph/Twitter card construction | Build new metadata through it, don't hand-roll `openGraph`/`twitter` objects | Use the standard Next.js `Metadata` type / `generateMetadata()` API directly |
   | Structured-data helper | A module with JSON-LD generator functions (e.g. `generate*Schema`) | Reuse an existing generator, or add a new one there following its shape | Build the JSON-LD object directly per the relevant [schema.org](https://schema.org/) type and render via `<script type="application/ld+json">` |

   Search for the underlying pattern (`generateMetadata`, `metadata =`,
   `ld+json`, `@type`) rather than guessing a specific file path - naming
   conventions differ between projects.

2. **Resolve the target.** Map what the user names (a URL path, a post
   slug, a page title, "the homepage," "all the tools pages," "the whole
   site") to the App Router route file(s) and/or content file(s) that
   render it (location varies by project - could be `app/`, `src/app/`, a
   CMS-backed collection, etc). If they name several or say "the whole
   site," list every target before starting.

3. **Load the guidelines.** Read `references/seo-guidelines.md` in full.
   It's organised by topic (crawlability, sitemap, URLs, titles &
   descriptions, headings, images, internal linking, structured data,
   mobile) each with Do/Don't items - that structure is the checklist you
   check against.

4. **Audit each target** against both the checklist and what step 1 found
   for this project:
   - **Metadata export** - does the route have `export const metadata` or
     `generateMetadata()`? Title 50-60 chars, description 150-160 chars,
     both unique to the page/post, built through the project's shared
     metadata helper if one exists, otherwise the standard `Metadata` API
     directly - never hand-rolled `openGraph`/`twitter` objects when a
     helper already exists.
   - **Canonical & indexing** - relies on `metadataBase` in the root
     layout plus per-page `path`; confirm nothing accidentally sets
     `robots: noindex` on a page meant to be indexed, and that anything
     genuinely private (drafts, gated content) does set it.
   - **Sitemap presence** - if it's a new static route, is it listed in
     the project's sitemap generator? If it's a new content type
     (posts/categories/etc already handled), does it need its own mapped
     block, following how existing content types are mapped there?
   - **Heading structure** - single `<h1>` per page, logical nesting,
     keywords used naturally rather than stuffed.
   - **Images** - descriptive `alt`, explicit `width`/`height` (or `fill`
     with a sized container) via `next/image`, filenames that describe
     content rather than `IMG_1234`.
   - **Internal linking** - descriptive anchor text, not "click here" /
     "read more".
   - **Structured data** - does the page/post's content type have a
     matching generator in the project's structured-data helper (if it has
     one)? If yes, is it wired in and rendered via a
     `<script type="application/ld+json">` with `JSON.stringify`? If the
     content type has no generator yet, add one following the existing
     generators' shape (or, if there's no structured-data helper at all,
     build the JSON-LD object directly per schema.org) before using it in
     the page.
   - **URL structure** - hyphens not underscores, lowercase, no
     unnecessary query params for canonical content.

5. **Multiple targets: parallelize with agents.** When reviewing more than
   one page/post, spawn one agent per target (Explore for a quick
   code-only pass), launched together in a single message so they run in
   parallel. Give each agent: the target's route/post file path, the full
   content of `references/seo-guidelines.md`, and what step 1 found for
   this project's SEO conventions (agents don't share your context), plus
   the checklist from step 4. Collect all results and merge into one
   report. Don't spawn agents for a single-target review - do that inline.

6. **Report or fix.**
   - If asked to **audit/review/check**, report findings first: for each
     issue give the file/location, what's wrong, the guideline violated,
     severity (`blocker` - missing/duplicate title or description, no
     `<h1>`, page wrongly noindexed; `major` - missing structured data for
     a content type that has a generator, broken canonical, missing alt
     text; `minor` - suboptimal length, non-descriptive anchor text), and
     the concrete fix. Group by target, severity first. Don't restate the
     whole guidelines doc - only the items actually violated.
   - If asked to **fix/add/optimize** something specific (e.g. "add
     structured data to this post," "fix the meta description"), implement
     directly using the existing helpers/generators above - no need to
     audit the whole page first unless the request is ambiguous about
     scope.

## Boundaries

- This covers technical and on-page SEO plus structured data. It is not a
  content-quality or backlink audit (the two biggest ranking factors, but
  outside what a codebase review can verify), not a Core Web Vitals/
  performance audit (defer to Lighthouse or a dedicated performance pass),
  and not a full accessibility audit - mention accessibility issues spotted
  in passing but don't expand scope to cover them.
- Don't edit `references/seo-guidelines.md` as part of a review. The user
  maintains it separately; only touch it if they explicitly ask to update
  the guidelines themselves.
- Don't invent a new structured-data generator, or hand-write a fresh
  metadata block, for a one-off need without first checking whether the
  project already has a shared helper that covers it.
- Don't add `hreflang`/multi-locale guidance unless the project actually
  has (or is growing) multiple locales - most projects this skill runs
  against are single-language.
