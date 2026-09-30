# Design Brief: tiffanyjhopkins.com refresh

Status: **DRAFT.** Facts marked *(from crawl)* will be filled in once the Playwright crawl runs. Owner: Tiffany J. Hopkins. Build deadline: 2026-10-29.

## What we're asking for

**Two distinct visual directions** for the site's look and feel. We will choose one, then it gets built as a static Astro site. The information architecture and URLs are fixed, so design effort goes into identity, type, color, layout and mood, not into restructuring.

## Background

tiffanyjhopkins.com is being moved off Squarespace to a fast, static site. The tagline on the home page is *"Looking for what's unseen."* The site covers several areas of the owner's work and interests:

- Mediumship and creative practice: events, circles, classes (`/mediumship-events`, `/unseen`)
- Reading and listening lists: `/library`, `/booklists`, `/listen`
- Everyday and personal pages: `/kitchen`, `/favorites-2`, `/the-aunt-mary`, `/book`, `/incoming`
- A business design portfolio (`/office`), now out of date and getting a notice that points to nobox.us and LinkedIn *(the owner intends to update it later)*

The current design is a Squarespace template with Typekit fonts. Screenshots of every page will be added to `design/current/` *(from crawl)*.

## Goals

1. Feel like one person's coherent practice, not a template. The tone should suit "looking for what's unseen": thoughtful, a little uncanny, warm, not corporate.
2. Hold very different content types under one system: event archive, reading lists, a portfolio, short personal pages.
3. Make the out-of-date `/office` page feel intentional, with a notice that reads as a clear, graceful pointer to current work rather than an error.
4. Be fast and readable on phones first.

## Fixed constraints

- **Pages and URLs do not change.** The page inventory is in `crawl/manifest.json` *(from crawl)*. Redesign the templates, not the sitemap.
- **Static site, no CMS.** No features that need a backend. Embeds are fine if justified.
- **Archive index:** `/mediumship-events` is now one archive list (old individual event pages redirect to it). Design that list to scale to ~70 items (grouping by year, filters optional).
- **Accessibility:** WCAG 2.2 AA contrast, visible focus states, readable line lengths, respects reduced motion.
- **Performance:** target Lighthouse mobile at least 90. Prefer system fonts or at most two self-hosted web fonts; no autoplaying media.
- **Images:** existing photos and illustrations are reused *(inventory from crawl)*. Say what treatment each direction gives them (crops, aspect ratios, captions).

## Page templates to design (per direction)

| Template | Used by |
|---|---|
| Home | `/` and `/home` |
| Content page (text + images) | `/unseen`, `/unseen/ethics`, `/kitchen`, `/the-aunt-mary`, `/book`, `/incoming`, offerings and model pages |
| List / library | `/library`, `/booklists`, `/favorites-2`, `/listen` |
| Archive list | `/mediumship-events` |
| Portfolio | `/office`, including the notice banner at the top |
| Global | header/nav, footer, 404 |

The final template mapping will be confirmed against the crawl.

## Deliverables (per direction)

1. A short rationale: mood, references, what it says about the work (a paragraph).
2. Home and one content page, desktop and mobile.
3. The `/office` page with the notice banner, desktop and mobile.
4. The archive list at scale (about 70 rows).
5. Design tokens: color palette (light, and dark if proposed), type scale with named fonts and licenses, spacing scale, radius, and any motion rules.
6. Component sketches: nav, footer, buttons and links, image with caption, banner.

Format: **TBD by the designer**: Figma, HTML/CSS prototype or static mockups. Tokens should be exportable as CSS custom properties or JSON.

## Timeline

| By | Milestone |
|---|---|
| 10/9 | Brief delivered |
| 10/16 | Two directions delivered |
| 10/17 | One direction chosen, with notes |
| 10/23 | Build complete on preview |

## Notice banner copy for `/office` (draft, for owner approval)

> This portfolio is out of date. For my current work, visit [nobox.us](https://nobox.us) or find me on [LinkedIn](LINKEDIN_URL_TBD).

## Open items

- LinkedIn URL.
- Whether the designer wants a light and dark theme.
- Any brand assets the owner wants preserved (logo, favicon, existing fonts).
- Reference sites or moods the owner likes or dislikes.
