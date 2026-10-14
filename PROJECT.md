# Alegria — Build Log

Chronological record of how the Alegria Shopify storefront was designed and built.
Newest entries are appended at the end.

---

## Project summary

- **Brand:** Alegria — a fictional premium furniture & home-interiors brand.
- **Goal:** a portfolio Shopify storefront demonstrating professional Online Store 2.0 theme development (Liquid, JSON templates, sections, snippets, metafields/metaobjects, filtering, Ajax cart, a11y, SEO, performance).
- **Platform:** Shopify (Online Store 2.0), theme base: **Dawn**.
- **Store:** `alegria-desoau8q.myshopify.com` (development store).
- **Live theme:** Horizon `#166705299504` (untouched).
- **Build theme:** "Alegria" (unpublished) `#166705823792`.

---

## Phase 1 — Inspect & plan

- Verified environment: no Shopify CLI initially → installed **Shopify CLI 4.9.3** (Node-based; no Ruby required).
- Confirmed Dev Dashboard org and created a new **development store** for Alegria.
- Confirmed storefront is password-protected (dev store private mode).
- Chose **Dawn** as the theme base; cloned it into `theme/` and pushed as an unpublished theme.
- Verified `shopify theme check` works (unlike earlier projects without the CLI).
- Data path: legacy custom apps are disabled for merchants (Jan 2026). Verified the admin's authenticated **internal GraphQL proxy** (`/api/shopify/<store>`) works for full Admin API access with no token to manage.

## Phase 2 — Design system & architecture

- Defined brand tokens in `config/settings_data.json`:
  - Palette: ivory `#F5F1EA`, sand `#ECE6DC`, charcoal `#23211E`, brass `#9A7B4F`, warm stone `#6E6555`.
  - Typography: **Playfair Display** (headings) + **Jost** (body).
  - Squared corners (2px), squared buttons, drawer cart, no hover zoom by default.
- Added `assets/alegria.css` (brand layer over Dawn) and wired it into `layout/theme.liquid`.
- Built custom sections:
  - `sections/alegria-hero.liquid` — full-bleed editorial hero (image, eyebrow, heading, text, CTA; height + position + overlay controls).
  - `sections/alegria-editorial.liquid` — reusable split image/text section (craftsmanship, room inspiration, brand story).
- Rebuilt the homepage `templates/index.json`: hero → craftsmanship → featured products → room inspiration → brand-story band.
- Validated with `shopify theme check` (0 errors) and pushed to the "Alegria" theme; verified rendering in preview.

### Brand palette (updated on review)
Crimson `#CE2029`, Charcoal `#232323`, Metallic Gold `#D4AF37` on an ivory base — five colour schemes in `settings_data.json`.

## Phase 3 — Catalog

- **AI imagery pipeline** (Gemini → Shopify Files → `shopify://shop_images/…`): generated 1 hero, 2 editorial and 16 product images in a consistent warm studio style.
- **5 collections** created and published: Seating, Tables & Desks, Lighting, Storage, Home Accessories.
- **16 products** created (real Shopify products) with options/variants, prices, SKUs, images and tags; published to the Online Store channel.
  - Seating: Larkin Lounge Chair, Marlow 3-Seat Sofa, Ottoline Accent Chair, Bramble Ottoman.
  - Tables & Desks: Oslo Dining Table, Linden Coffee Table, Wren Writing Desk.
  - Lighting: Vane Floor Lamp, Halo Pendant Light, Ember Table Lamp.
  - Storage: Rowan Shelving Unit, Kestrel Cabinet, Nook Sideboard.
  - Home Accessories: Aria Wool Throw, Terra Stoneware Vase, Luca Brass Mirror.
- Homepage wired to the real catalog; hero + both editorial sections use AI imagery.
- Admin API notes: this API version uses `productSet` (options + variants + prices) and `productCreateMedia`; `ProductInput.images`/`media` and `ProductVariantInput.optionValues` are not accepted. Products/collections must be published to the Online Store publication explicitly.

## Phase 4 — Structured content, editorial & cart

- **Metaobject** `alegria_material` (fields: Title, Description, Care) with 5 entries: Solid Oak, Walnut, Travertine, Wool & Linen, Brushed Brass.
- **Product metafield definitions** (namespace `alegria`): `material` (metaobject reference), `dimensions`, `assembly`, `warranty`. Values set on all 16 products (63 metafields); the Terra vase intentionally has no material to prove graceful missing-value handling.
- **Custom section** `sections/alegria-product-specs.liquid` renders the specifications (incl. the linked material metaobject) on the product page via `templates/product.json`.
- **Editorial:** a "Journal" blog with 3 published articles (small spaces, wood finishes, lighting).
- **Navigation:** main menu rebuilt with the five collections + Journal + Contact.
- **Cart:** Ajax cart drawer verified — adding a variant returns the correct item and subtotal ($1,890 for the Larkin chair).
- API notes: `articleCreate` uses `body` (not `contentHtml`) and `image { url }` only; articles publish automatically.

## Phase 5 — Quality assurance

- `shopify theme check`: **0 errors**.
- **axe-core:** homepage, collection and product pages clean — the only flagged node is the admin preview-bar iframe, which does not exist on the live store.
- **Fixed during QA:**
  - Critical: product quantity input had no accessible name → added `aria-label` in `sections/main-product.liquid`.
  - Unselected variant-pill label contrast (4.12) → darkened in `alegria.css`.
  - Inventory: `productSet` left some variants tracked at 0 (showing as sold out) → set all variants to "continue selling" + untracked.
- **Lighthouse (mobile, live theme):** Accessibility **97** · SEO **92** · Best Practices **73** · Performance **54** (FCP 4.8s, LCP 8.2s, CLS 0). Performance/BP are bound by Shopify platform JS.
- Responsive: no horizontal overflow at 390px; header, grids, product page and footer verified.

## Phase 6 — Documentation & portfolio

- `README.md` (overview, features, architecture, setup, testing, limitations).
- `docs/setup-guide.md` (metafield + metaobject definitions and how to recreate them).
- Screenshots in `docs/screenshots/`.
- **Theme published** as the live theme (approved); original Horizon kept as an unpublished backup.
- Live URL: https://alegria-desoau8q.myshopify.com/ (password-protected).
- Store settings otherwise unchanged; no credentials committed.

## Post-review refinements

- **Palette rebalanced** to charcoal ≈60% / crimson ≈30% / gold ≈10% — a dark, bold direction. Five colour schemes updated; announcement bar rebranded (crimson).
- **Broken links fixed:** created **Our Story** (`/pages/about`) and **Room Guides** (`/pages/rooms`) pages and repointed the editorial CTAs.
- **SEO:** set the shop homepage title/description (`global.title_tag`/`description_tag` shop metafields) and unique meta descriptions for all 16 products, 6 collections and both pages.
- **Product galleries:** generated **32 additional AI images** (a styled in-room lifestyle shot + a material detail shot per product) → every product now has a **3-image gallery**.
- **Accessibility:** fixed contrast on the crimson band (white text), product-spec labels (gold) and care text; the homepage, collection and product pages are **axe-clean** (only the admin preview-bar iframe is flagged).
- **Lighthouse (mobile, live):** Accessibility **100** · SEO **92** · Best Practices **73** · Performance **63**.

## Content & layout pass (round 2)

- **New hero image** and a **redesigned Room inspiration section** — the sparse two-column editorial was replaced with a new `alegria-room-inspiration` section: a heading + intro row above a 3-up grid of large room cards (reading corner / dining room / small living room), each with an image, caption and "Explore" link.
- **Real page templates:** created `templates/page.about.json` and `templates/page.rooms.json` (hero + editorial + rich-text / room cards) and assigned them to the pages via `templateSuffix`, replacing the bare Dawn text page.
- **Collection banners:** set an AI banner image on each of the 5 collections and enabled `show_collection_image`.
- **Colour balance:** the brand-story band is now a full crimson band; charcoal remains dominant.
- **Theme Check:** fixed the one warning in our code (unused assign); the remaining 9 are Dawn-base warnings.

### API-version limitations found
- **Variant-specific images** cannot be set programmatically: `ProductVariantsBulkInput`, `ProductVariantSetInput` and `ProductVariantInput` all reject an `imageId`/`mediaId` field in this Admin API version. The theme (Dawn) supports variant images natively — they must be assigned in the Shopify admin UI.
- A **logo image** was prototyped (AI wordmark) but not applied: the generated typeface did not match the theme's Playfair Display and the asset had heavy margins, so the branded **text wordmark** (shop name rendered in Playfair) was kept.

## Improvements pass (round 3)

- **Variant-specific images solved.** Discovered that `productVariantsBulkUpdate` accepts `mediaId` (the earlier failures were `imageId` on `ProductVariantInput`/`ProductVariantSetInput`, which this version rejects). Generated 10 colour/finish variant images and assigned each to its variant — the product gallery now switches image when a variant is selected (e.g. Larkin in Crimson Velvet / Ivory Bouclé / Charcoal Linen).
- **"Shop by collection"** section added to the homepage (5 collections, portrait cards) for faster discovery.
- **Product pages enriched:** added **Shipping & returns** and **Care & materials** accordion blocks alongside the metafield Specifications.
- Screenshots refreshed (`product-desktop.png`).
