# By The Book

A blackjack learning kit: a flashcard deck and two printable reference cards that take a
player from *"what do the cards mean"* all the way to counting and basic-strategy
deviations. Every number is computed, not remembered.

> Working name: **By The Book**. (Earlier drafts sealed the cards with a `GREASE`
> placeholder — that's gone now.)

## What's here

```
deck/
  blackjack-deck.json              65-card flashcard deck (tiers 1–3)
cards/
  blackjack-chart-card.html        full basic-strategy grid (hard / soft / pairs)
  blackjack-cheatsheet-simple.html the 12-rule quick sheet, no percentages
viewer/
  index.html                       flashcard deck viewer (reads deck/blackjack-deck.json)
```

Open either reference card directly in a browser. The **viewer** reads the deck over
`fetch()`, so it needs to be served rather than opened from `file://`:

```bash
python3 -m http.server      # from the repo root
# then open http://localhost:8000/viewer/
```

## The deck

`blackjack-deck.json` is an array of 65 cards. Each card:

| field | meaning |
|-------|---------|
| `id` | stable id, e.g. `t1-basics-004` |
| `tier` | 1 = fundamentals, 2 = core strategy, 3 = advanced (counting / deviations) |
| `category` | `rules`, `hard-total`, `soft-total`, `pair`, `trap`, `counting`, `deviation`, `bankroll`, `etiquette` |
| `front` / `back` | the question and the answer |
| `why` | the reasoning — *why* the play is correct, not just what it is |
| `stat` | a computed number that makes the play defensible (bust %, EV/unit, win/lose split) |
| `tags` | freeform labels for filtering |
| `ruleset_dependent` | true if the answer changes with table rules (S17/H17, DAS, surrender) |

The learning arc is deliberate: **cheat sheet → full strategy → trap hands → counting.**
Tier 1 (14 cards) is vocabulary and table rules; tier 2 (40) is the strategy grid one
decision at a time, including 12 `trap` cards for the hands people reliably misplay
(16 v 10, 12 v 2/3, soft 18 v 9); tier 3 (11) is Hi-Lo counting and the common
deviations.

### On the numbers

The `stat` figures come from an infinite-deck EV model (stands soft 17). That's within a
hair of 4–8 deck reality — fine for teaching, and they should be labeled *approximate* on
the finished product. Examples carried in the deck:

- 16 v 10: *stand wins 23% / loses 77%; hit busts 62%; surrender caps the loss at −0.50*
- Doubling 11 v 6: *+0.67 / unit*
- A natural: *~4.7% of hands, about 1 in 21*

## The reference cards

**`blackjack-chart-card.html`** — the canonical basic-strategy grid for **4–8 decks, stands
soft 17, double after split, late surrender**. Hard totals, soft totals, and pairs, color
coded H / S / D / P / R with a legend. Built from a JS data table so the rules are easy to
audit and re-skin.

**`blackjack-cheatsheet-simple.html`** — the on-ramp. Twelve plain-language rules, **zero
percentages**. Led by the one idea that drives half of basic strategy: **weak dealer (2–6)
vs strong dealer (7–A)**. Same visual system as the full card, so they read as a set.

Both cards share a type system (Fraunces / IBM Plex) and a brass-on-bone "maker's plate"
look, sealed `BS` (Basic Strategy) and `QS` (Quick Strategy).

## The deck viewer

`viewer/index.html` is a standalone flashcard app over the deck, in the same brass-on-bone
plate aesthetic. It's data-driven — point it at a different deck JSON and it re-skins
itself.

- **Flip** a card (click, Space, or Enter) to reveal the answer, the *why*, and the
  computed *stat*.
- **Filter** by tier (1–3) and by category; the category list and counts are built from the
  deck at load.
- **Shuffle** the current selection; **navigate** with the buttons or ← / →.
- **Track progress**: mark each card *Got it* (`g`) or *Need review* (`a`). Learned cards
  are remembered in `localStorage` and shown with a ✓ and a progress bar. *Reset progress*
  clears it.

It degrades gracefully: opened from `file://` (where `fetch()` is blocked) it shows the
exact command to serve the repo instead of failing silently.

## Provenance & notes

These assets were authored alongside the Workback project but are unrelated to it; this
repo is their proper home. The Workback repo itself carries no blackjack code.

Fixed on import: one deck card reported the dealer busting *"2910%"* of the time — a
mangled figure, now corrected to the real ~28% average bust rate.

### Still open

- **Brand**: "By The Book" is a working name; the seals now read `BY THE BOOK`.
- Label the EV stats *approximate* in any shipped UI (infinite-deck model).
- The viewer is a v1: flip, filter, shuffle, progress. Natural next steps — a spaced-
  repetition order, a "review only unlearned" mode, and a quiz that hides the answer and
  scores your call.
