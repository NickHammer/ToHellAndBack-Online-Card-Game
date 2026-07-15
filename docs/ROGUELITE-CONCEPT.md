# To Hell and Back: Roguelite Mode — Concept Exploration

*Exploration branch. Nothing here is committed design; it's a thinking space for a
single-player, run-based mode built on the existing Oh Hell engine
(`src/shared/engine.ts` + `src/shared/ai.ts`).*

## The pitch

You died. To get back, you play your way **down through the nine circles of hell and back
up through the spheres of heaven** — one hand of Oh Hell per stop. The deeper you go, the
crueler the table; the climb back up is where your earned powers shine. It's the game's
existing 1-up-and-back-down structure, literalized into a Dante-shaped run.

Balatro proved a solo card roguelite lives or dies on: (1) a tight core loop you already
want to replay, (2) run-warping items, (3) escalating stakes with rest stops. Oh Hell's
bid-exactly mechanic is a great core loop — tense, skill-forward, luck-textured.

## Run structure

```
DESCENT (hell)                          ASCENT (heaven)
Circle 1  · 1 card    vs 2 demons      Sphere 1 · 9 cards
Circle 2  · 2 cards                    Sphere 2 · 8 cards
   …           …                          …          …
Circle 9  · 9 cards   vs 3 demons      Sphere 9 · 1 card   → Paradise (win)
        └── THE BOTTOM: 10 cards, boss demon, special rules ──┘
```

- **One hand per stop** (not a full 19-hand game). A run that goes the distance is 19
  hands — same shape as the base game, new meaning.
- **Gate to advance**: make your bid to descend/ascend. Miss it and you lose **grace**
  (run health). Grace starts at 3; at 0 the run ends.
- **Every 3rd circle is a shop/rest**: spend souls (currency earned per made bid,
  scaled by bid size — bold bids pay more) on relics, or restore 1 grace.

## Opponents: demons with table quirks

Reuse `ai.ts` bots, but each demon warps one rule while it's seated:

| Demon | Quirk |
|---|---|
| The Glutton | Takes every tie (plays after you, wins rank ties) |
| The Hoarder | You can't see how many tricks it has taken |
| The Liar | Its bid is hidden until the hand ends |
| The Usurer | Missing your bid costs double grace here |
| The Tempter | Offers +2 souls if you bid one higher than you want |

Boss at the bottom: **The Adversary** — trump suit changes every 3 tricks.

## Relics (the run-warping layer)

Earned at circles, bought at shops. Examples, roughly sorted by power:

- **Loaded Die** — once per hand, see the trump card before bidding is locked.
- **Grave Ledger** — see a running count of played trumps.
- **Devil's Thumb** — once per hand, replay a card you just played (before the next player acts).
- **Halo (cracked)** — bids may miss by one, once per circle, without losing grace.
- **Ferryman's Coin** — skip a circle outright (consumed).
- **Sinner's Arithmetic** — hook rule applies to demons, never to you.
- **Second Soul** — +1 max grace.

Relics should bend *information* and *bid tolerance* first — the fun of Oh Hell is
judgment under uncertainty, so power = better information or softer punishment, not
"win tricks automatically."

## Scoring / meta

- Run score = souls banked + circles cleared + grace remaining.
- Unlocks between runs (new relics in the pool, new demons) — light meta-progression,
  no power creep across runs.
- Daily seed mode falls out for free: the engine already takes an injectable RNG
  (`Rng` in `engine.ts`), so a shared seed gives everyone the same deals/demons.

## What the engine already gives us

- `newGame` / `startNextHand` with configurable `maxHandSize` — per-stop hand sizes are
  just a config away.
- Injectable RNG for seeded runs (`shuffle(deck, rng)`).
- `legalBids` hook — demon quirks and relic effects are mostly bid/legality filters.
- Bots (`chooseBid` / `chooseCard`) as the demon baseline; quirks wrap these.

## What it needs that doesn't exist

- A **run state** wrapper above `GameState` (grace, souls, relics, current circle).
- Client screens: map (descent/ascent track), shop, relic tray, run summary.
- Single-player only at first — no server needed; the whole run can live client-side,
  which also means it works offline and costs nothing to host.

## Open questions

- Does one hand per circle feel too thin? Alternative: best-of-3 tricks-style "duels"
  in early circles, full hands deeper.
- Trump manipulation as the relic axis vs. bid tolerance as the relic axis — pick one
  as primary, or the pool feels mushy.
- Art/tone: keep the felt-table look with hellish palette shifts per circle (cheap,
  atmospheric), or full theme swap?
- Name: "To Hell and Back" already *is* the roguelite name. The base game could become
  "table mode" within it.
