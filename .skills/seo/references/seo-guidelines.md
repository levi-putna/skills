# SEO Guidelines

Maintained checklist for the `seo` skill. Edit this file directly to add,
remove, or reword guidelines - the skill re-reads it on every run, so
changes take effect immediately without touching `SKILL.md`.

Each item is written as a Do/Don't so it's checkable against a real
page or post, not a general principle. Written for any Next.js App Router
project; where a rule needs a raw HTML example, the Next.js equivalent
(metadata export, `next/image`, the App Router's `sitemap` file convention)
is the one that actually applies. File/function names below (`sitemap.js`,
`generateMetadata`, etc.) refer to Next.js's own conventions, not to any
specific project's helper modules - `SKILL.md`'s discovery step is what
finds the actual project-specific helpers to check against.

## 1. Crawlability

- **Do** confirm the project's `robots.js`/`.ts` (or equivalent) allows
  crawling of the target route. If the project has no `robots.js`, Next.js
  serves a permissive default - note that explicitly rather than assuming
  rules exist.
- **Do** leave `robots: { index: true, follow: true }` as the implicit
  default; only set `noindex` explicitly on pages meant to stay out of
  search results (drafts, gated/password-protected posts, internal tools).
- **Don't** add a page that should be discoverable without also adding it
  to the project's sitemap generator - crawlers can still find it via
  links, but it won't get sitemap priority/change-frequency signals.
- **Do** rely on `metadataBase` (set once in the root layout) plus a
  relative `path` per page rather than hardcoding the full origin in every
  page's metadata - keeps canonical/OG URLs correct if the domain ever
  changes.

## 2. Sitemap

- **Do** add a new *static* route to the project's sitemap generator (the
  App Router's `sitemap.js`/`.ts` file) with a sensible `changeFrequency`
  and `priority` (1.0 homepage, 0.8-0.9 primary sections, 0.6-0.7
  secondary, 0.3 legal pages like privacy/terms).
- **Do** map a new *content type* (a new collection of posts/pages) the
  same way existing content types are mapped in that file - a
  slug-listing helper feeding a `.map()` block, not a hand-maintained list
  of URLs.
- **Don't** include `noindex` pages, drafts, or duplicate/parameterised
  URLs in the sitemap - only canonical, indexable URLs belong there.
- **Do** keep `lastModified` accurate (actual publish/update timestamps,
  not just "now" on every build) where the data is available, since it's
  a real signal for re-crawl frequency.

## 3. URL structure

- **Do** use hyphens, lowercase, and keyword-bearing slugs
  (`/blog/why-feature-flags-rot`), matching however the project already
  generates slugs from titles.
- **Don't** let a slug run so long it becomes unreadable - trim filler
  words rather than keeping the full title verbatim.
- **Don't** introduce query-string-driven canonical content (e.g.
  `/post?id=123`) - the App Router's file-based routing already avoids
  this by default; don't undo it with client-side param-driven content.

## 4. Titles & meta descriptions

- **Do** give every route a unique `export const metadata` (static routes)
  or `generateMetadata()` (dynamic routes) - never rely on the root
  layout's default title/description for content pages.
- **Do** keep titles to 50-60 characters and descriptions to 150-160
  characters; Google truncates past that, so the truncation itself
  shouldn't cut off the important part.
- **Do** build social metadata through the project's shared metadata
  helper if one exists, rather than writing `openGraph`/`twitter` objects
  inline on every page - keeps site name, handle, and image-alt
  conventions consistent everywhere. If no such helper exists yet, note
  that as a gap once you're touching more than a couple of pages.
- **Don't** duplicate the page's `<h1>` verbatim as the title if a more
  compelling, keyword-forward phrasing reads better in a search result -
  they can differ.
- **Do** use any content-level title/description overrides (e.g. post
  frontmatter fields) when present instead of always falling back to the
  page's default title/excerpt.

## 5. Heading structure

- **Do** use exactly one `<h1>` per page - the page/post title - with
  `<h2>`/`<h3>` nested logically underneath for sections and subsections.
- **Don't** skip heading levels (an `<h2>` straight to an `<h4>`) purely
  for visual sizing - use CSS for visual weight, headings for structure.
- **Do** let headings read as natural section labels; keyword presence
  should fall out of writing clearly about the topic, not be forced in.

## 6. Images

- **Do** use `next/image` with explicit `width`/`height` (or `fill` inside
  a sized container) - this is also a Core Web Vitals (CLS) concern, not
  just SEO.
- **Do** write `alt` text that describes what the image actually shows;
  empty `alt=""` is correct only for purely decorative images.
- **Don't** ship an image with a meaningless filename (`IMG_2024.png`,
  `asset-final-v2.png`) when it's being newly generated or added -
  descriptive filenames are a minor but free signal.
- **Do** confirm background/social images referenced in metadata actually
  resolve - a broken OG image kills link-preview click-through even when
  the metadata is otherwise correct.

## 7. Internal linking

- **Do** use descriptive anchor text ("read the deployment guide") instead
  of "click here" / "read more" - both for users scanning the page and for
  the link's own keyword signal.
- **Do** link related posts/pages where genuinely relevant (via whatever
  related-content mechanism the project already has) rather than leaving
  posts as orphaned pages with no inbound internal links.
- **Don't** create link farms of unrelated cross-links purely to
  manufacture internal link count - relevance matters more than volume.

## 8. Structured data (JSON-LD)

- **Do** check the project's structured-data helper (if it has one) for an
  existing generator before writing a new schema.
- **Do** add a new generator to that same module, following its existing
  shape (`@context`, `@type`, plain-object return), if a page's content
  type genuinely has no match yet (e.g. a Product or Event schema). If the
  project has no structured-data helper at all, build the JSON-LD object
  directly per the relevant [schema.org](https://schema.org/) type.
- **Do** render JSON-LD via a `<script type="application/ld+json">`
  containing `JSON.stringify(schema)`.
- **Don't** hand-roll a raw `@context`/`@type` object inline in a page
  component when a generator already exists for that type - route new
  fields through the generator so every page of that type stays
  consistent.
- **Do** validate non-trivial structured data changes with the
  [Google Rich Results Test](https://search.google.com/test/rich-results)
  or [Schema.org Validator](https://validator.schema.org/) before treating
  the change as done.

## 9. Mobile & Core Web Vitals (light touch)

- **Do** confirm the page doesn't rely on hover-only affordances to reveal
  content search engines (or mobile users) need to see - a mobile-first
  crawl won't trigger `:hover`.
- **Don't** duplicate a full responsive-layout audit here - hand off to
  the `ui-responsive-layout-audit` skill for breakpoint/touch-target/
  content-priority review; this skill only flags mobile issues that
  directly gate indexing (e.g. content hidden entirely on mobile that a
  mobile-first crawler would then treat as the primary version).

## SEO audit checklist (quick reference)

### Critical
- [ ] Route allowed for crawling (or intentionally `noindex`'d)
- [ ] Unique title (50-60 chars) and description (150-160 chars)
- [ ] Single `<h1>` per page
- [ ] Listed in the sitemap generator if it's a new indexable static route

### High priority
- [ ] Canonical resolves correctly via `metadataBase` + `path`
- [ ] Open Graph / Twitter card metadata present via the shared helper (if
      one exists) or the standard `Metadata` API
- [ ] Images have `alt`, explicit dimensions, and resolve correctly
- [ ] Structured data present for content types that have a generator

### Medium priority
- [ ] Descriptive internal link anchor text
- [ ] Descriptive, hyphenated, lowercase URL slug
- [ ] Related-content links present where relevant

### Ongoing
- [ ] Re-check the sitemap generator after adding a new static section
- [ ] Validate structured data after schema changes
- [ ] Watch Search Console for crawl errors and indexing drops after a
      metadata refactor

## Tools

| Tool | Use |
|------|-----|
| Google Search Console | Monitor indexing, fix crawl issues |
| Google Rich Results Test | Validate JSON-LD structured data |
| Schema.org Validator | Validate JSON-LD against the schema spec |
| Lighthouse | Broader SEO + performance audit |

## References

- [Google Search Central](https://developers.google.com/search)
- [Schema.org](https://schema.org/)
