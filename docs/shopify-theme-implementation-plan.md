# Custom Shopify Theme — Implementation Plan & Directives for Claude

Base: `Shopify/skeleton-theme` (official) — confirmed structure:
```
assets/     → CSS, JS, images, fonts
blocks/     → reusable, nestable UI components (native theme blocks)
config/     → theme settings schema (settings_schema.json, settings_data.json)
layout/     → theme.liquid (top-level page wrapper)
locales/    → translation files
sections/   → modular full-width page components (with {% schema %})
templates/  → JSON templates composing sections per page type
```
This plan assumes you're driving a Claude Code session inside this repo, iterating section by section. It's organized so you can paste each phase (or even each numbered directive) to Claude as its own task.

---

## 0. Before Writing Any Code: Lock the Design Brief

Claude Code will produce noticeably better, more consistent output if it's given a written design brief *once*, up front, that it can refer back to every session — rather than re-explaining "premium homeware brand" in every prompt. Create this as an actual file in the repo, e.g. `docs/BRAND_BRIEF.md`, and tell Claude to treat it as a standing constraint.

**Directive to give Claude first:**

> Read and internalize `docs/BRAND_BRIEF.md` before doing any design or styling work in this repo. Every section, component, and style decision must be consistent with it. If a request from me conflicts with it, flag the conflict before proceeding.

Fill in `BRAND_BRIEF.md` with, at minimum:
- Brand name, one-paragraph positioning statement, tone of voice (3 adjectives)
- Locked color palette (hex values, named — e.g. `--color-clay`, `--color-ink`, `--color-cream`)
- Typography pairing (headline serif + body/UI sans, with fallback stacks and weights actually licensed/available)
- Spacing/grid rhythm (base unit, e.g. 8px scale)
- Photography direction (lighting, styling, aspect ratios you'll actually have assets for)
- The 5 site-wide non-negotiables from our research: (1) generous whitespace, (2) one primary CTA per screen, (3) lifestyle imagery paired with every product image, (4) specific/no generic trust copy, (5) collections and patterns are *named*, never SKU-labeled

---

## 1. Phase-by-Phase Build Plan

### Phase 1 — Design Tokens & Global Foundations
**Goal:** every visual primitive exists as a reusable token before any section is built.

Directives:
1. "Create `assets/base.css` with CSS custom properties for the full design system: colors, font families/sizes/line-heights on a type scale (e.g. 1.25 ratio), spacing scale (8px base), border-radius scale, shadow tokens, and transition/easing tokens. Pull exact values from `docs/BRAND_BRIEF.md`. Do not hardcode any of these values elsewhere in the theme — always reference the custom property."
2. "Set up `config/settings_schema.json` so merchants can override the core brand colors and font selections from the Theme Editor, but keep spacing/radius/motion tokens as fixed CSS variables not exposed as settings — those are brand-system decisions, not per-merchant preferences."
3. "Build a base typographic scale in `base.css`: h1–h6, body, small, and a distinct 'eyebrow/label' style (uppercase, letter-spaced, small) for use above section headings — this label style is core to the editorial feel we want across the site."
4. "Implement a 12-column responsive grid utility and container widths (mobile, tablet, desktop, wide-desktop breakpoints) as reusable CSS, not per-section reinvented layout."

### Phase 2 — Global Layout: Header, Nav, Footer, Cart Drawer
Directives:
1. "Build the header section with: logo (left), primary nav (center or left-aligned depending on brand brief), and icon cluster (search, account, wishlist, cart) right-aligned. Support a mega-menu block type for the primary nav — each top-level item should support: a text-link column list, a featured-image tile, and an optional 'shop by pattern' or 'shop by room' sub-list, since our IA depends on cross-cutting navigation (category / pattern / room / occasion), not just category."
2. "Make the mega-menu content editable via section blocks/metaobjects so non-developers can update it without code changes."
3. "Add a slim, dismissible announcement bar above the header (for seasonal collection launches / free shipping threshold) as its own section, not hardcoded into the header."
4. "Build the cart drawer (not a full page redirect) with line-item thumbnails, quantity steppers, a free-shipping progress bar, and a 'complete the set' upsell slot showing 2-3 products tagged with the same pattern/collection as items already in cart."
5. "Build the footer with: full category link list, About/Story, Sustainability/Materials, Help links, social icons, payment icons (small, muted, bottom-most), and an editorial-style newsletter signup block — not a bare email input, style it as a small design moment with a short value-prop line."

### Phase 3 — Homepage Sections
Build each as its own section file with a schema so merchants can reorder/configure via the Theme Editor. Use this exact module order (from our research) as the default `templates/index.json`, but each section should be independently reusable elsewhere.

1. **`sections/hero.liquid`** — "Full-bleed image or video hero, one headline, one short subhead, one primary CTA button. Support an optional secondary muted text-link CTA. No carousel/slider by default — if multiple hero slides are needed, cap at 2 and auto-advance slowly (6s+), never more."
2. **`sections/category-grid.liquid`** — "4-6 large image tiles (category or room), label overlaid bottom-left on a subtle gradient scrim for legibility, using the eyebrow label style from Phase 1."
3. **`sections/featured-collection-story.liquid`** — "An editorial block: large image + a text column with an eyebrow label, collection name, 2-3 sentence story blurb, and a CTA — mirrors how Papier/The Citizenry introduce a themed collection rather than just a product grid."
4. **`sections/shop-by-pattern.liquid` / `sections/shop-by-room.liquid`** — "A second cross-cut merchandising rail of the same catalog, using collection metafields (see Phase 5) rather than duplicate manual collections."
5. **`sections/brand-proof-strip.liquid`** — "3-4 columns, icon + specific stat/claim + one line of supporting copy (not generic trust badges — pull actual claims from BRAND_BRIEF, e.g. 'Printed to order — zero overstock')."
6. **`sections/bestsellers-ugc.liquid`** — "Product/UGC grid pulling from a 'featured reviews with photos' source (app or metaobject-backed) — must support real customer images, not just star ratings."
7. **`sections/journal-teaser.liquid`** — "3-post editorial teaser grid pulling from the blog, styled as magazine cards (image, category label, title, 1-line excerpt) — no 'read more' buttons, whole card is the link."
8. **`sections/newsletter.liquid`** — "Styled signup module, value-led copy, positioned before the footer, visually distinct background from surrounding sections."

**General directive for all homepage sections:** "Every section must expose spacing/padding-top/bottom as block-level or section-level settings so merchants can control rhythm without code changes, but default values should follow the 8px spacing scale from Phase 1."

### Phase 4 — Collection Pages
Directives:
1. "Build `sections/collection-intro.liquid`: an editorial banner (image + 2-3 sentence story text) that renders above the product grid on collection pages, populated by the collection's description/metafield — this is what separates a 'curated collection' feel from a plain filtered product list."
2. "Build filtering by pattern/collection-name, color family, and room in addition to standard price/availability filters, using Shopify's native filter/sort (`{% paginate %}` + `filter` object) — do not hand-roll filtering in JS if the native storefront filtering API covers it."
3. "Inject a full-width lifestyle-image tile into the product grid every 12-16 products (a 'shop the room' break) — implement as a section block type so merchants control frequency and image content."
4. "Ensure grid item cards show: product image (with hover-swap to a second lifestyle/detail image), name, pattern/collection name as a small label, price, and a compact color/pattern swatch row if variants exist."

### Phase 5 — Product Page (PDP)
Directives:
1. "Build the media gallery to support a mix of studio product shots and lifestyle/in-room/texture images in one gallery, with thumbnails below or beside on desktop, swipeable on mobile."
2. "Add a materials/quality block as its own section (weight, composition, care instructions, made-to-order note) — pull copy from product metafields so it's structured, not buried in the free-text description."
3. "Build a 'Shop the Pattern' or 'Complete the Set' cross-sell section pulling other products sharing the same pattern/collection tag/metafield — this is the highest-leverage merchandising feature for a POD catalog, prioritize getting this right."
4. "For rug/mat-type products: add a size guide modal/drawer and, if feasible, a simple room-scale visual aid (illustration comparing rug sizes to common furniture, doesn't need to be a live AR tool)."
5. "Reviews section must support photo uploads and display photo-reviews with visual priority over text-only ones."
6. "Sticky add-to-cart bar on scroll (mobile especially) once the primary buy box scrolls out of view."

### Phase 6 — Content & Trust Pages
Directives:
1. "Build a flexible page template (`templates/page.about.json` or similar) composed of reusable sections — story/text-image blocks, stat/proof strip, timeline or process steps — so 'Our Story', 'How It's Made', and 'Sustainability' pages can all be assembled from the same section library rather than one-off custom pages."
2. "Build a blog/article template with the magazine-card styling established in Phase 3's journal teaser, plus a clean long-form article layout (readable measure, ~65-75 character line length, generous line-height)."

### Phase 7 — Performance, Accessibility, Polish
Directives:
1. "Audit and keep `critical.css` limited to only what's needed for above-the-fold render on the homepage and PDP — everything else loads deferred, per the Skeleton theme's existing convention."
2. "All images must use responsive `srcset`/`sizes` via Shopify's `image_url`/`image_tag` filters, lazy-loaded except the hero/first-fold image."
3. "Run an accessibility pass: color contrast against the locked palette (flag any brand colors that fail WCAG AA for text use and propose a text-safe variant), keyboard navigability of the mega-menu and cart drawer, alt text fields required on all image-driven sections."
4. "Motion should be subtle and consistent: define 2-3 standard transition durations/easings in Phase 1 tokens and reuse them everywhere (hover states, drawer open/close, image hover-swap) — no bespoke easing per component."
5. "Test Theme Editor experience end-to-end: every section/block should be fully configurable by a non-developer merchant without breaking layout at empty/edge states (no image selected, long product titles, out-of-stock items, empty collections)."

### Phase 8 — QA & Launch Checklist
- Lighthouse/PageSpeed pass on home, collection, and PDP templates (mobile + desktop)
- Cross-browser check (Safari iOS especially, since lifestyle-heavy sites are image-weight-sensitive)
- Empty-state check for every dynamic section (no reviews yet, no cross-sell matches, sold-out product)
- Full keyboard-only navigation pass
- Merchant-editability pass: have someone unfamiliar with the code attempt to reorder homepage sections and edit the mega-menu in the Theme Editor

---

## 2. Metafield / Taxonomy Setup (do this before Phase 3)

This is what powers "shop by pattern," "shop by room," and PDP cross-sell cheaply, using Shopify's native data model instead of custom apps:

| Metafield | Applies to | Purpose |
|---|---|---|
| `custom.pattern_name` | Product | Named pattern/collection (e.g. "Meadow") — powers cross-sell and pattern nav |
| `custom.color_family` | Product | e.g. "Terracotta," "Sage," "Ink" — powers color-family filtering |
| `custom.room` | Product | e.g. "Bedroom," "Entryway," "Office" — powers room nav/cross-cut collections |
| `custom.collection_story` | Collection | 2-3 sentence editorial blurb rendered by `collection-intro.liquid` |
| `custom.made_to_order_note` | Product | Standard made-to-order copy block, structured not buried in description |

**Directive to give Claude:** "Before building navigation or cross-sell sections, confirm these metafield definitions exist in the store's metafield schema; if not, generate the metafield definition JSON/setup instructions so I can create them in Shopify Admin, and write all Liquid to read from them rather than parsing tags or titles."

---

## 3. Master Kickoff Prompt (paste this first in your Claude Code session)

> I'm building a custom Shopify theme for a premium print-on-demand homeware brand, starting from the official `Shopify/skeleton-theme` repo. I have a written design brief at `docs/BRAND_BRIEF.md` and an implementation plan at `docs/IMPLEMENTATION_PLAN.md` (this document). Work through the plan in order, phase by phase — don't skip ahead to polish before foundations exist. Before building any section, check whether reusable tokens/utilities already exist in `assets/base.css` and use them rather than introducing new one-off values. Every section needs a `{% schema %}` block enabling full Theme Editor configurability. Flag any point where a directive in the plan seems to conflict with Shopify theme best practices or with `BRAND_BRIEF.md`, rather than silently choosing one. After each phase, summarize what was built and any open decisions before moving to the next phase.

---

## 4. Suggested File/Doc Setup in the Repo

```
docs/
  BRAND_BRIEF.md          ← Section 0 above, filled in with your actual brand decisions
  IMPLEMENTATION_PLAN.md  ← this document
assets/
  base.css                ← Phase 1 tokens + typography + grid utilities
sections/
  hero.liquid
  category-grid.liquid
  featured-collection-story.liquid
  shop-by-pattern.liquid
  shop-by-room.liquid
  brand-proof-strip.liquid
  bestsellers-ugc.liquid
  journal-teaser.liquid
  newsletter.liquid
  collection-intro.liquid
  ...
```

Keep `BRAND_BRIEF.md` and this plan as living documents — update them as decisions firm up, and re-point Claude at the updated versions each session rather than re-explaining verbally.
