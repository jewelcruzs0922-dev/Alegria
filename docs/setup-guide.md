# Alegria — Shopify setup guide

This document records the Shopify Admin configuration that the Alegria theme depends on, and how to recreate it.

## Store

- **Store:** `alegria-desoau8q.myshopify.com` (development store)
- **Theme:** "Alegria" (Dawn-based, customised) — published as the live theme
- **Storefront:** password-protected (dev store private mode)

## Collections

Created as manual collections and published to the **Online Store** channel:

| Title | Handle |
| --- | --- |
| Seating | `seating` |
| Tables & Desks | `tables-desks` |
| Lighting | `lighting` |
| Storage | `storage` |
| Home Accessories | `home-accessories` |

## Products

16 products with options/variants (e.g. Upholstery, Size, Finish, Colour), SKUs, prices and images. All are published to the Online Store channel.

## Metaobject definition — Material

| Field | Key | Type |
| --- | --- | --- |
| Title | `title` | Single line text |
| Description | `description` | Multi-line text |
| Care | `care` | Multi-line text |

**Type:** `alegria_material`

Entries: **Solid Oak**, **Walnut**, **Travertine**, **Wool & Linen**, **Brushed Brass**.

## Product metafield definitions

Namespace `alegria` (owner type: **Product**):

| Name | Key | Type | Notes |
| --- | --- | --- | --- |
| Material | `material` | Metaobject reference | Validated against `alegria_material` |
| Dimensions | `dimensions` | Single line text | e.g. `W 220 × D 100 × H 75 cm` |
| Assembly | `assembly` | Multi-line text | e.g. `No assembly required.` |
| Warranty | `warranty` | Single line text | e.g. `10-year warranty.` |

The Terra Stoneware Vase intentionally has **no** `material` value, to verify the section hides missing fields cleanly.

## How to recreate in Shopify Admin

1. **Content → Metaobjects → Add definition**
   - Name `Material`, type `alegria_material`, add fields Title/Description/Care as above.
   - Add the five entries.
2. **Settings → Custom data → Products → Add definition** (repeat for each metafield)
   - Namespace `alegria`, keys/types as per the table.
   - For `material`, set the validation to reference the `alegria_material` definition.
3. Populate each product's metafields (Settings → Custom data, or a product's **Metafields** section).

## Rendering

`sections/alegria-product-specs.liquid` reads `product.metafields.alegria.*` and renders a specification block on the product page. The Material metaobject reference is dereferenced to show its title, description and care text. Empty values are skipped.

## Notes

- Products and collections created via the Admin API are **not published** by default — they must be added to the Online Store publication.
- The theme itself requires no metafields to render; the specifications section simply hides when no values exist.
