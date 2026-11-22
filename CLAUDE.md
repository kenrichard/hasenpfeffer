# Hasenpfeffer

The Pennsylvania Dutch double-deck euchre game: you and a partner against two computer players,
with bidding, hoss / double hoss, bowers, a kitty, hints, and three difficulty levels.

Started in Claude Duet and moved here on 2026-11-22. The source was the claude.ai artifact
https://claude.ai/artifact/SBwdpEKMBHpjePLvpWzVon (version of 2026-11-21).

## Files

- `index.html`: the whole game in one page. The first `<script>` is the engine (pure game logic
  and computer players); it exports through `module.exports` when loaded in Node, so a server or
  tests can reuse it. The second script is the browser client.

## Deck

Ken's family plays with **only J, Q, K, A, from four decks**: 64 cards, four of every card,
15 each, 15 tricks, 4 left over as the kitty, game to 64. That is the default (`deck: 'J4'`,
`target: 64`). No low cards: don't switch the default back to 7–A. Settings also offer 64 cards
from two decks of 7–A, 48 cards (9–A, 12 each) and 32 cards (J–A from two decks, 8 each).

The number of decks is `copies` in the `DECKS` table in `derive()`. The computer players' bidding
thresholds are set per deck there too (`ai: { hoss, dhoss, safety }`). The J4 and 32-card numbers came from simulating games: on the 32-card deck the original numbers made computers
hoss on about 60% of hands and fail most of them, so scores sank and games never ended. If you
change bidding or the deck, rerun a simulation that plays full games and check that they finish.

## Hand review

At the end of each hand, "Review my hand" lists the choices where you differed from the tutor
(which plays like the Normal computer). The solo controller records each of your decisions with
the tutor's pick (`g.log`; card plays keep a snapshot of the game). For card plays,
`HP.compareCards` plays both cards against 60 sampled deals of the unseen cards and the review
lists the play only if the tutor's card did clearly better: at least 0.25 tricks, or 8 points
better odds of making the bid (or setting the other team). Bids, trump and discards show the
tutor's reasoning with no score. Solo only: online games run on the server.

## State

- `window.HP_STANDALONE = true` puts the page in solo-only mode. Open `index.html` in a browser
  to play. Saved games and settings live in localStorage (`hp.game`, `hp.settings`, `hp.level`,
  `hp.hints`).
- The two-player table mode (`OnlineController`) connects over WebSocket to `/ws` on the same
  host. That server is not in this folder yet: it lived in Claude Duet and has to be brought over
  or rewritten before two-player play works.
