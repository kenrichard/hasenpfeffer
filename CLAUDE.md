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

Ken's family plays 15 cards each, 15 tricks, game to 64: two decks of 7 through ace (64 cards),
4 left over as the kitty. That is the default (`deck: '64'`, `target: 64`). Settings also offer
48 cards (9–A, 12 each) and 32 cards (J–A, 8 each, bids 4 to 6).

The computer players' bidding thresholds are set per deck in `derive()` (`ai: { hoss, dhoss,
safety }`). The 32-card numbers came from simulating games: the big-deck numbers made computers
hoss on about 60% of hands and fail most of them, so scores sank and games never ended. If you
change bidding or the deck, rerun a simulation that plays full games and check that they finish.

## State

- `window.HP_STANDALONE = true` puts the page in solo-only mode. Open `index.html` in a browser
  to play. Saved games and settings live in localStorage (`hp.game`, `hp.settings`, `hp.level`,
  `hp.hints`).
- The two-player table mode (`OnlineController`) connects over WebSocket to `/ws` on the same
  host. That server is not in this folder yet: it lived in Claude Duet and has to be brought over
  or rewritten before two-player play works.
