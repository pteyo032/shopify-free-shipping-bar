# Gotchas

Technical pitfalls discovered while building this, so you don't re-hit them.

## The animation needs zero JavaScript — because of how the theme re-renders the cart

Horizon's cart drawer re-renders via the Section Rendering API on every cart
mutation, but it doesn't replace the DOM wholesale — it **morphs** it
(`assets/morph.js`), patching attributes on existing nodes instead of
recreating them. That means a plain CSS `transition: width …` on the fill
bar animates correctly across cart updates, since the element persists
across the re-render. If you port this to a theme that does a full
innerHTML replace on cart update instead of morphing, the fill will jump
instantly with no animation — you'd need to capture the old width in JS
before the swap and animate it back in manually.

## Dollars vs. cents

Tier amounts are entered by the merchant as plain numbers (e.g. `50`,
meaning $50), but `cart.items_subtotal_price` is in the shop's smallest
currency unit (cents). The snippet converts tier settings with `| times: 100`
before comparing — if you add more amount-based settings, remember this
conversion or every threshold comparison will be off by 100x.

## Keep the marker DOM stable across renders

Each tier's marker is only rendered if that tier is configured (non-blank,
`> 0`). Which tiers are valid is driven entirely by **theme settings**, not
by cart contents — so the marker DOM shape never changes between cart
updates within a single page session (it only changes if the merchant edits
settings, which triggers a full page reload anyway). This is what makes the
CSS-only animation reliable: don't make marker visibility depend on cart
state, or you'll reintroduce the "node replaced, not morphed" problem above.

## Segmented fill, not one continuous bar

Early versions computed a single fill percentage against the last tier's
amount. That looks wrong once tiers are close together: the fill visibly
overshoots past an already-reached tier's marker toward the next one, which
reads as a bug even though the math is "correct" for a single continuous
bar. The fix: each tier gets its own fill segment, capped so it stops
exactly at that tier's marker once reached
(see `prev_amount_N` / `segment_width_N` in the snippet). The next segment
then starts filling from zero.

## Marker label collisions at tight tier spacing

If two tiers are close together on the track and their reward labels are
wide, centering both labels on their own marker makes the text overlap
(`white-space: nowrap` makes this worse, not better). The snippet caps each
label to a `max-width` and lets it wrap instead of forcing one line — still
not perfect at extreme configurations (e.g. 3 tiers within a few dollars of
each other with long labels), but far more robust than a fixed single-line
label.

## Dot and label must be positioned independently

If you center a marker's dot-plus-label as a single flex column, the block's
combined vertical center — not the dot itself — lands on the bar's line,
so the dot visually floats above the line instead of sitting on it. Position
the dot and the label independently (both anchored to the same point, the
dot centered on it, the label offset below it) so the dot always sits
exactly on the track regardless of the label's height.
