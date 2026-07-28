# Integration guide

This bar is a single snippet plus a handful of theme settings — no section,
no block, no JavaScript. It's designed to be dropped into the cart drawer of
a **Shopify Horizon** theme (or a Horizon-based theme with the same cart
drawer structure).

## 1. Copy the files

- `snippets/free-shipping-bar.liquid` → your theme's `snippets/` folder
- `assets/icon-truck.svg`, `assets/icon-gift.svg` → your theme's `assets/`
  folder (skip these if your theme already ships icons with these exact
  names, or rename the `case` branches in the snippet to match your own)

## 2. Add the settings

Open your theme's `config/settings_schema.json` and find (or create) the
settings group for your cart (in Horizon, the group named `t:names.cart`).
Paste the contents of [`settings-schema-snippet.json`](settings-schema-snippet.json)
into that group's `"settings"` array.

Then add the matching translation keys to your locale files — see
`locales/en.default.json`, `locales/en.default.schema.json`, `locales/fr.json`
and `locales/fr.schema.json` in this repo for the exact keys and copy
(English + French included; add more languages as needed, Shopify falls back
to the default locale for any language you don't translate).

## 3. Render the snippet in your cart drawer

In `snippets/cart-drawer.liquid`, add `{% render 'free-shipping-bar' %}` in
**two places** so it shows in both the empty-cart state and the
has-items state:

- Inside your empty-cart markup, right after the "your cart is empty" title
- Right after your cart drawer's header (title + item count), before the
  scrollable list of cart items — not inside the scrollable area, so the bar
  stays visible while the customer scrolls

Exact anchor points will differ slightly if you're not on stock Horizon —
search for where the drawer's title/header is rendered and drop the render
call immediately after it.

## 4. Configure it

In the theme editor, go to **Theme settings → Cart** and scroll to the
**Free shipping bar** section:

- Toggle it on/off
- Set up to 3 tiers: an amount, a reward label, and an icon (truck / gift /
  discount / none) — leave a tier's amount blank to skip it
- Under **Style**: bar color, bar thickness, marker (dot) size, icon size
- Progress message and success message, with `[amount]` and `[reward]`
  placeholders

## What this bar does *not* do

It's a display-only progress indicator. It does not apply any discount, add
a free gift to the cart, or change the shipping rate. See the "Known
limitation" section in the README before you configure tier amounts —
you'll need matching Shopify Discounts / Shipping rates for the rewards to
actually apply at checkout.
