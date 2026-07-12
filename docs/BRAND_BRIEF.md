# BRAND_BRIEF.md

This is the single source of truth Claude should read before any design/styling work. Every value below is expressed as a token in Phase 1 (`assets/base.css`) — change it once here, then once in the CSS variables, and it propagates everywhere. Nothing below should ever be hardcoded into an individual section file.

Everything here is a considered starting point, not a permanent decision — it's all placed in one file specifically so you can swap any single piece later without touching layout/component code.

---

## 1. Brand Positioning (placeholder — fill in when ready)

**Name:** [TBD]
**One-line positioning:** A made-to-order home goods brand for people who want their space to feel curated, not stocked — soft, livable pattern and color across blankets, bedding, rugs, and everyday objects.
**Tone (pick 3, these are a reasonable starting set):** *Warm, considered, unfussy.*
— Warm: never cold-minimalist or clinical; language and visuals should feel like a well-loved home, not a showroom.
— Considered: specific claims, named collections, no filler marketing language ("premium quality!!").
— Unfussy: approachable and easy, closer to Papier's friendly editorial voice than to a stiff luxury-brand register.

*(This section is the easiest to revisit once you've used the site for a bit — everything downstream, especially voice in copy, should be re-checked against whatever you finalize here.)*

---

## 2. Color Palette — "Soft Warm" system (Papier-inspired)

Papier and Ruggable both lean on a warm, desaturated palette rather than stark white/black or saturated brights — soft blush, clay, sand, and muted sage read as "considered" without tipping into twee. Below is a full working system, not just a mood board: every role a color needs on an ecommerce site is covered.

| Token | Hex | Role |
|---|---|---|
| `--color-bg` | `#FBF6F0` | Primary page background — warm off-white, not stark white |
| `--color-surface` | `#F4EBE2` | Card/section backgrounds, alternating section bands |
| `--color-ink` | `#2E2A26` | Primary text — warm near-black, softer than pure `#000` |
| `--color-ink-muted` | `#6B6259` | Secondary text, captions, metadata |
| `--color-clay` | `#C17A5D` | Primary accent / CTA color — warm terracotta |
| `--color-clay-dark` | `#A6603F` | CTA hover/active state |
| `--color-blush` | `#E7C3B8` | Secondary accent — soft dusty rose, decorative bands, tags |
| `--color-sage` | `#9CA986` | Tertiary accent — muted warm sage, use sparingly (badges, seasonal accents) |
| `--color-sand` | `#E4D9C9` | Borders, dividers, input backgrounds |
| `--color-white` | `#FFFFFF` | Pure white — reserve for product photography backgrounds and rare high-contrast moments, not the page background |

**Usage rules to give Claude:**
- "`--color-bg` is the default page background everywhere, not white. Use `--color-surface` for banding between sections instead of hard borders."
- "`--color-clay` is the only primary-action color — one CTA color, used consistently. Do not introduce a second 'button color.'"
- "`--color-sage` is an accent, capped at small usage (badges, seasonal callouts) — it should never dominate a full section background."
- "Product photography white backgrounds may use pure white (`--color-white`) even though the page background is warm — that contrast is intentional and keeps product images crisp."
- "Run every text/background pairing through a contrast checker; `--color-blush` and `--color-sand` are backgrounds only, never text colors, since they won't pass contrast at small sizes."

**How to change this later:** swap the 10 hex values in `base.css` and every section/component updates automatically — nothing else needs to change. If you want a cooler or more saturated direction later, keep the same *roles* (bg/surface/ink/primary accent/secondary accent/tertiary accent/border) and just swap hex values into them.

---

## 3. Typography Pairing

Goal: an editorial serif for headlines that gives the "designed collection" feel (like Papier's headline treatment and Rifle Paper Co's editorial confidence), paired with a clean, warm-leaning sans for body/UI so the site stays legible and fast — not precious. Both recommended fonts are free (Google Fonts / Fontshare) so there's no licensing friction while you're iterating.

| Role | Font | Notes |
|---|---|---|
| **Headline / display** | **Fraunces** (variable, Google Fonts) | Soft-serif with warmth and a bit of character in its curves — reads editorial and premium without feeling stiff or overly formal like a classic luxury serif (e.g. Didot). Use at 400-500 weight for most headlines; the optical-size/"soft" axis can be dialed toward more personality for hero moments and dialed back toward crisp for smaller headings. |
| **Body / UI** | **Work Sans** (Google Fonts) | Humanist sans, warmer and rounder than Inter/Helvetica, stays highly legible at small sizes for product info, filters, cart, etc. |
| **Eyebrow / label style** | Work Sans, uppercase, letter-spaced (~0.08em), small size, `--color-ink-muted` | Used above section headings and on category labels — this is the recurring "editorial label" device referenced in the implementation plan. |

**Fallback stacks (for Claude to implement in CSS):**
```css
--font-display: "Fraunces", Georgia, "Times New Roman", serif;
--font-body: "Work Sans", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
```

**Alternate pairing options** (if you try the above and want a different feel later — swap the whole pairing, don't mix-and-match a new serif with the old sans or vice versa):
- Warmer/rounder alternative to Fraunces: **Lora** (more traditional editorial serif, slightly more conservative/classic — closer to Papier's actual body of work)
- Cleaner alternative to Work Sans: **General Sans** (Fontshare, free) — slightly more neutral/modern, less "friendly," if you want the UI to feel a touch more Quince-minimal

**Type scale (8px-based rhythm, Phase 1 tokens):**
```
--text-xs: 12px    (legal, fine print)
--text-sm: 14px    (captions, metadata, labels)
--text-base: 16px  (body)
--text-lg: 20px    (subheadings, lead paragraphs)
--text-xl: 28px    (section headings)
--text-2xl: 40px   (page/collection titles)
--text-3xl: 56px   (hero headline, desktop only — scale down on mobile)
```

---

## 4. Spacing & Grid Rhythm

- Base unit: **8px**, scaling `4 / 8 / 16 / 24 / 32 / 48 / 64 / 96 / 128`
- Section vertical padding default: `96px` desktop / `56px` mobile between major homepage modules — generous space is one of the cheapest "premium" signals available, don't compress it to fit more content above the fold.
- Container max-width: `1440px`, with a `24px` mobile / `48px` desktop side gutter
- Grid: 12-column, `24px` gutters desktop, `16px` mobile

---

## 5. Border Radius, Shadow & Motion Tokens

| Token | Value | Use |
|---|---|---|
| `--radius-sm` | `4px` | inputs, small tags/badges |
| `--radius-md` | `12px` | product cards, buttons |
| `--radius-lg` | `24px` | large image modules, modals |
| `--shadow-soft` | `0 4px 24px rgba(46,42,38,0.08)` | cards, dropdowns, drawers — kept subtle, warm-tinted (uses `--color-ink` at low opacity, not generic black) |
| `--ease-standard` | `cubic-bezier(0.4, 0, 0.2, 1)` | default transition easing |
| `--duration-fast` | `150ms` | hover states, small UI feedback |
| `--duration-base` | `300ms` | drawer open/close, image hover-swap |

Rounded-but-restrained corners (12px on cards, not fully pill-shaped) read as soft and warm without tipping into "app UI." Shadows stay soft and warm-tinted rather than harsh/gray, consistent with the palette.

---

## 6. Photography Direction

- Natural daylight, warm color temperature (avoid cool/blue-cast lighting) — matches the warm palette.
- Every product needs at minimum: one clean studio/on-white shot (for grid thumbnails) + one in-room lifestyle shot (for PDP gallery and homepage modules).
- Styling props stay within the brand palette family (warm neutrals, clay, blush, sage accents) so lifestyle photography and site UI feel like one continuous world — this is the detail that most separates "brand" from "print shop" per our earlier research.
- Avoid generic Printify mockup backgrounds where possible; if using platform-generated mockups short-term, keep a consistent crop/angle/background across a whole collection so it reads as art-directed rather than assorted.

---

## 7. Five Non-Negotiables (site-wide)

1. Generous whitespace — don't compress section padding to fit more above the fold.
2. One primary CTA color and style per screen — `--color-clay`, consistently.
3. Every product image is paired with at least one lifestyle image somewhere in its presentation.
4. Trust/quality copy is specific ("printed to order, zero overstock," "OEKO-TEX certified inks") — never generic badge language.
5. Patterns and collections are named ("Meadow," "Terra") — never SKU- or number-labeled.

---

## 8. Open Decisions Log

Keep this list updated as you firm up decisions — it tells Claude what's still flexible vs. locked:

- [ ] Brand name + logo treatment
- [ ] Final confirmation on Fraunces vs. Lora for display font (try both rendered with real product photos before locking)
- [ ] Whether `--color-sage` survives as an accent or gets dropped in favor of a two-accent system (clay + blush only)
- [ ] Photography approach: commissioned lifestyle shoot vs. styled Printify mockups vs. AI-generated placeholders for launch
