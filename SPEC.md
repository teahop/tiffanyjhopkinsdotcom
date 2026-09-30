# Spec: tiffanyjhopkins.com rebuild (Astro + Cloudflare Pages)

Status: **DRAFT, awaiting review.** Phase 1 (Specify) of spec-driven development. No implementation until approved.
Hard deadline: **2026-10-29** (DNS cutover).

## Objective

Move tiffanyjhopkins.com off Squarespace onto a static Astro site hosted on Cloudflare Pages, with:

1. **URL parity.** Every public URL keeps working, either as the same page or as a 301 redirect.
2. **Content parity.** Text, metadata and images are pulled from the live site by a Playwright crawl. Images are self-hosted, not hotlinked from `images.squarespace-cdn.com`.
3. **A refreshed look and feel.** A designer proposes two directions (see `DESIGN-BRIEF.md`), one is chosen, and the build follows it.
4. **`/office` kept, with a banner.** The portfolio page stays, with a note at the top that it is out of date and pointing to nobox.us and LinkedIn for current work. The owner plans to update it later.

Users: visitors to Tiffany's site (mediumship and creative practice, book lists, listening and kitchen pages, past business design portfolio). Owner: Tiffany, who wants a low-maintenance static site.

Success looks like: the owner reviews a `*.pages.dev` preview, the redirect and parity checks pass, and DNS moves before 10/29 with no broken inbound links.

## Decisions already made

| Topic | Decision |
|---|---|
| Repo | `github.com/teahop/tiffanyjhopkinsdotcom`, public |
| Local folder | `~/tiffanyjhopkinsdotcom` |
| `/mediumship-events/*` (~70 URLs, many duplicate or truncated slugs) | Collapse to an archive index; 301 old slugs to it |
| `/office` | Keep; add out-of-date banner linking nobox.us and LinkedIn |
| Design | Designer proposes 2 directions |
| Launch | Preview first, cut over when approved, deadline 10/29 |
| Blog `/unknown-unknown` | Owner says it isn't public; crawl to verify (see Open Questions) |
| Squarespace features (forms, scheduling, commerce) | Owner believes none are used; crawl to verify |

## Assumptions (correct me or I proceed)

1. Static output only, no CMS. Content lives in Markdown/JSON in the repo; edits are made by asking Claude or by editing files.
2. Cloudflare Pages is connected to the GitHub repo (build on push to `main`, preview on PRs).
3. The site stays on the apex domain `tiffanyjhopkins.com`. `www` redirects to the apex, or the reverse, matching current behavior.
4. Modern browsers only. No IE11.
5. No analytics at launch unless the current site has some (the crawl will report it).
6. Squarespace `robots.txt` blocks many AI crawlers. The new site keeps an equivalent policy unless the owner says otherwise.

## Tech stack

- Astro (latest stable), static output (`output: 'static'`), TypeScript strict
- Astro content collections for events and any blog posts
- Astro `<Image>` / `astro:assets` for image optimization
- Playwright (Node) for the crawl and for post-build checks
- Cloudflare Pages, plus a `_redirects` file for 301s
- Node LTS (**not currently installed on this machine**, see Open Questions)

## Commands

```
Install:      npm install
Dev:          npm run dev
Build:        npm run build            # astro check && astro build
Preview:      npm run preview
Crawl:        npm run crawl            # Playwright -> content/ + public/images/ + crawl/manifest.json
Verify URLs:  npm run verify:urls      # every URL in crawl/manifest.json returns 200 or an expected 301
Verify links: npm run verify:links     # no broken internal links or missing images in dist/
Deploy:       git push origin main     # Cloudflare Pages builds automatically
```

## Project structure

```
SPEC.md                    This document
DESIGN-BRIEF.md            Brief for the designer
tasks/plan.md, todo.md     Plan and task list (Phase 2 and 3)
scripts/crawl.mjs          Playwright crawler
scripts/verify-*.mjs       URL, redirect and link checks
crawl/manifest.json        Every URL found: source, status, type, target (page | redirect | drop)
crawl/raw/                 Raw HTML snapshots (gitignored if large)
src/pages/                 Routes (one per preserved URL)
src/layouts/               Base layout, page layout
src/components/            Header, footer, banner, event list, etc.
src/content/               Markdown/JSON content extracted by the crawl
src/styles/                Tokens and global CSS (from the chosen design direction)
public/images/             Self-hosted images from the crawl
public/_redirects          Cloudflare Pages 301 rules
public/robots.txt          Robots policy
```

## Code style

Plain Astro components, small and typed, tokens for all colors, type and spacing.

```astro
---
// src/components/Banner.astro
interface Props { tone?: 'notice' | 'warning' }
const { tone = 'notice' } = Astro.props;
---
<aside class={`banner banner--${tone}`} role="note">
  <slot />
</aside>
```

- Files: `PascalCase.astro` for components, `kebab-case` for routes and content files.
- No inline styles. No client-side JS unless a page needs it.
- Every image has real `alt` text (the crawl carries over existing alt/caption text; gaps are flagged).

## Testing strategy

- **Parity (automated):** `verify:urls` walks `crawl/manifest.json` against the built site (or the preview) and fails on any URL that isn't 200 or an expected 301.
- **Link integrity (automated):** `verify:links` fails on broken internal links or missing local images.
- **Content diff (semi-automated):** a script compares crawled text against rendered page text and reports pages that differ beyond a threshold.
- **Visual (manual):** owner reviews desktop and mobile on the preview.
- **Quality bar (proposed):** Lighthouse mobile at least 90 for Performance, Accessibility, SEO; WCAG 2.2 AA color contrast; no layout shift on load.

## Boundaries

- **Always:** keep the crawl manifest current; run `npm run build` and `verify:*` before merging; keep canonical URLs, titles and meta descriptions from the live site unless a redirect requires otherwise; use the tokens, not hard-coded values.
- **Ask first:** adding npm dependencies; changing any URL or redirect target; adding third-party embeds or analytics; touching DNS or Cloudflare account settings; anything that publishes or sends content outward.
- **Never:** commit secrets or API tokens; hotlink Squarespace CDN images in production; drop a URL without a redirect; delete a page from the manifest without the owner's approval; crawl aggressively (one request at a time with a delay, honoring robots.txt).

## Success criteria

1. 100% of URLs in `crawl/manifest.json` return 200 or the expected 301 on the preview.
2. `/mediumship-events` shows the archive index; every old `/mediumship-events/<slug>` 301s to it, with no redirect chains or loops.
3. `/office` renders the original portfolio content with the out-of-date banner (links to nobox.us and LinkedIn) at the top.
4. Every crawled text block and image appears on its page, or is listed in the manifest with a reason it was dropped.
5. No image is served from `images.squarespace-cdn.com`.
6. Titles, meta descriptions, canonical tags and OG tags match the live site (or are intentionally improved and noted).
7. A generated `sitemap.xml` and `robots.txt` are present.
8. Lighthouse mobile at least 90 on Performance, Accessibility and SEO for home, one content page and `/office`.
9. Owner signs off on the preview, and DNS cuts over on or before **2026-10-29**.

## Rough timeline (to be confirmed in the plan)

| Window | Milestone |
|---|---|
| By 10/2 | Spec approved; Node and Playwright set up; crawl done; manifest reviewed |
| By 10/9 | Design brief delivered to the designer |
| By 10/16 | Two design directions received; one chosen |
| By 10/23 | Build complete on preview; parity checks green |
| 10/23 to 10/27 | Owner review, fixes, redirect verification |
| By 10/29 | DNS cutover |

Risk: the design step gates the visual build. Mitigation: build a token-driven, unstyled-but-solid structure first, so the chosen direction lands as a theme layer. If the design slips, ship content parity with the current look approximated, and the redesign follows as a second release.

## Open questions

1. **Node install.** Node is not installed. OK to install Node LTS with a user-level version manager (fnm) so I can run Astro and Playwright?
2. **LinkedIn URL** for the `/office` banner.
3. **Blog.** `/unknown-unknown` (posts like "normalize-talking-to-the-dead", tags, categories) is in the Squarespace sitemap, so it is publicly reachable. You said you don't see it as public. Should we (a) keep the posts, (b) redirect the whole section to the home page or a page you name, or (c) drop it? I'll confirm after the crawl shows whether it is linked from navigation.
4. **Designer.** Who receives the brief (you, an external designer, another AI tool), and what format do they want back (Figma, mockups, HTML prototype)?
5. **Cloudflare.** Is the domain's DNS already on Cloudflare, or is it at Squarespace? This affects the cutover steps and `www` handling.
6. **Hidden or unlisted pages** the crawl can't discover (for example password-protected pages or unlinked pages you want to keep).
7. **Robots policy.** Keep the current AI-crawler blocks?
8. **Analytics.** None, or keep whatever exists today?
